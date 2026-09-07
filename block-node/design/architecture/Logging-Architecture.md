# Block Node Logging Architecture

## Abstract

This document is the single reference for **how logging works in the Block Node and how to
do it right**. It captures the framework, the on-disk layout, the level policy, the coding
patterns, and the design decisions (with the alternatives we discarded). It is intended both
as an onboarding reference for engineers and agents new to the project.

Companion documents:

- [`docs/logging-guidelines.md`](../../logging-guidelines.md) — the per-level definitions engineers apply when writing a log line.
- [`docs/block-node/logging.md`](../../block-node/logging.md) — operator reference: how to change levels and read logs.
- [`docs/block-node/troubleshooting.md`](../../block-node/troubleshooting.md) — the operator playbook.

## Goals / Non-goals

**Goals.** One agreed level policy; INFO that an operator can actually run the node from;
per-class level control; low overhead on hot paths; text logs that stream cleanly into Loki /
ELK / Splunk.

**Non-goals.** Structured/JSON event logging; per-block audit trails in logs (that is what
metrics and stored blocks are for); a bespoke logging framework.

## Architecture at a glance

|           Concern            |                                                 Choice                                                 |
|------------------------------|--------------------------------------------------------------------------------------------------------|
| Application logging API      | `java.lang.System.Logger` (JDK facade, OTEL-compatible)                                                |
| Backend                      | `java.util.logging` (JUL) — no Logback/Log4j2/SLF4J as primary                                         |
| Helidon logs                 | Bridged into JUL via `io.helidon.logging.jul` (runtime only)                                           |
| Third-party SLF4J logs       | Bridged into JUL via `log4j-slf4j2` (runtime only, plugin modules)                                     |
| Console format (local)       | `CleanColorfulFormatter` — ANSI-coloured, one line per event                                           |
| Console + file format (prod) | `java.util.logging.SimpleFormatter` — plain, one line per event                                        |
| Level control                | JUL `.level` properties (global / package / class), file selected by `-Djava.util.logging.config.file` |

Every class obtains its logger the same way — `System.getLogger(getClass().getName())` — so
the **fully-qualified class name is the correlation key** for both the log format and the
level configuration. Bootstrap happens once in the `BlockNodeApp` constructor: if
`-Djava.util.logging.config.file` is set (Docker/K8s) the JVM has already loaded it and the
app leaves it alone; otherwise the app loads the bundled `logging.properties` from the
classpath and applies the colourful console formatter. Helidon's own logging bootstrap is
disabled so it defers to this configuration.

## Filesystem & configuration layout

There is **one config format** (JUL `logging.properties`) with a per-environment copy. Which
file is live is decided entirely by `-Djava.util.logging.config.file`.

| Environment |                Config file (source of truth)                |                                                      Live path / injection                                                       |                                           Output                                            |
|-------------|-------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| Local / jar | `block-node/app/src/main/resources/logging.properties`      | Loaded from classpath at startup                                                                                                 | Console (stderr), coloured                                                                  |
| Docker      | `block-node/app/docker/logging.properties`                  | Mounted at `/opt/hiero/block-node/logs/config/logging.properties`; flag set in `JAVA_TOOL_OPTIONS` by `docker/update-env.sh`     | Console + rotating file `/opt/hiero/block-node/logs/blocknode-%g.log` (50 MB × 15 ≈ 750 MB) |
| CI          | `block-node/app/docker/ci-logging.properties`               | Same flag mechanism                                                                                                              | Console only                                                                                |
| Kubernetes  | `charts/block-node-server/values.yaml` → `blockNode.logs.*` | Rendered into a ConfigMap (`configmap-logging.yaml`), mounted at `/opt/hiero/block-node/logs/config`; flag set by `_helpers.tpl` | Console + rotating file on a dedicated `logging` PVC (`/opt/hiero/block-node/logs`)         |

## Level policy (the decision to ratify)

Levels use `System.Logger.Level`, mapped to JUL as: `TRACE→FINEST`, `DEBUG→FINE`,
`INFO→INFO`, `WARNING→WARNING`, `ERROR→SEVERE`. **Production enables INFO and above only.**

|  Level  |                                         Use it for                                         | Frequency  |    Audience    |
|---------|--------------------------------------------------------------------------------------------|------------|----------------|
| ERROR   | System entering a failure state; needs immediate attention (data-loss risk, cannot serve). | Rare       | On-call, paged |
| WARNING | Unhealthy but not failing; recoverable; may signal a defect or attack.                     | Uncommon   | On-call        |
| INFO    | Low-frequency, operationally-significant state changes an operator needs in production.    | Infrequent | Operators      |
| DEBUG   | Detail for diagnosing hard problems in test — or, on demand, targeted in prod.             | High       | Developers     |
| TRACE   | Very detailed, per-item developer/unit-test diagnostics.                                   | Very high  | Developers     |

**The rule that settles the discussion — metrics for progress, logs for problems.**

- Per-item progress and success (blocks received / verified / persisted, rates, latencies,
  queue depth) belong in **metrics**, never in per-item INFO logs. This matches
  `logging-guidelines.md` ("avoid logs in loops — use a metrics library") and the observability
  three-pillars model: metrics answer *how many / how fast / how healthy*, logs answer *what
  happened and why*.
- **Success is never an INFO log.** INFO is reserved for infrequent state changes (startup,
  shutdown, config, plugin lifecycle, the heartbeat below).
- **One-time lifecycle events stay at INFO** — application startup/shutdown, the effective-config
  dump, the plugin load list, and port binding are infrequent and operationally significant, so
  they remain INFO (a *failure* during startup/shutdown is WARNING/ERROR, but the normal
  milestone is INFO). See [`docs/logging-guidelines.md`](../../logging-guidelines.md).
- **Log-only operators are still served**, two ways:
  1. **One periodic aggregate heartbeat at INFO** — a single low-frequency line (default 5 min)
     emitted from the aggregating layer (the `server-status` plugin, which already knows the
     available range and next-expected block): `Status heartbeat: oldestBlock=X newestBlock=Y
     nextExpected=Z`. This is the recognised *heartbeat / summary logging* pattern — it is not
     a per-block log, so it does not violate the rule above.
  2. **Per-package DEBUG on demand** — an operator can raise one package/class to `FINE`
     temporarily to get full per-block detail during an incident, then revert (see
     [`docs/block-node/logging.md`](../../block-node/logging.md)).

## Open discrepancy: recoverable failures sit between INFO and WARNING

The review of PR #3308 surfaced a genuine gap in
[`logging-guidelines.md`](../../logging-guidelines.md) that this project should reconcile
explicitly. The guidelines define the two ends cleanly but leave the middle ambiguous:

- **WARNING** "almost always triggers a call to the on-call operations engineer … if the
  triggering event is not sufficient to warrant … alerting the on-call staff, then that event
  is most likely a `DEBUG` or lower level."
- **INFO** is "unequivocally useful to an operations engineer," infrequent, and "does not
  indicate a problem or potential problem."

A large class of events falls between these two: a **recoverable operation failure** — a
retried S3 upload, a skipped malformed block header, a failed staging-file cleanup, a
re-queued backfill block. By common sense and general industry best practice these are
**`WARNING`s**: something *did* go wrong and an operator generally wants to see it, even though
the system recovered on its own. That is exactly why this PR originally raised them to
`WARNING`. The tension is that the project's guidelines reserve `WARNING` for on-call-paging
conditions, and `INFO` is defined as "does not indicate a problem" — so under a literal reading
of *our* guidelines these recoverable failures fit neither level cleanly.

The maintainer discussion on #3308 chose to resolve the ambiguity toward **`INFO`** for now:
that keeps `WARNING` strictly on-call-actionable (so a WARNING/SEVERE grep stays a clean "needs
attention" filter) while still giving log-only operators visibility into recoverable failures.
This PR follows that consensus and reverts the recoverable caught-exception logs from `WARNING`
back to `INFO`. Genuine on-call signals are still kept at `WARNING` — *all backfill sources
unreachable* (aggregate) and an *uncaught exception in the server-status heartbeat thread* — and
echoes of failures already logged at their origin (the backfill persistence/verification awaiter
observers, the backfill verification-failed notice) are lowered to `DEBUG` to avoid a second
signal for the same event.

**Impact / follow-up:** `INFO` for recoverable failures is a deliberate, documented deviation
from the common-sense/best-practice expectation that a recovered failure is a `WARNING`. It is a
direct consequence of this project defining `WARNING` narrowly as "pages the on-call engineer";
teams that treat `WARNING` as the normal home for recovered-but-notable failures would classify
most of these the other way. `logging-guidelines.md` should be amended to make this explicit —
either broaden `WARNING` to cover recovered-but-notable failures (the best-practice default), or
add a fourth category that formally routes recoverable failures to `INFO` — so future reviews
apply a single rule instead of re-litigating each call site. Tracked as follow-up to #3278.

## Patterns & conventions (how to do it right)

- **Parameterised, deferred messages** — `LOGGER.log(INFO, "Node [{0}] started with score [{1}]", nodeId, score)`. Never build the string eagerly (`+` concatenation, `.formatted(...)`) unless an exception is also being logged.
- **Log the exception object**, not just its message: `LOGGER.log(WARNING, "Failed to X: {0}", id, e)` where `e` is the trailing `Throwable`, so the stack trace is preserved.
- **One line, ≤ ~200 chars.** No multi-line events, no method calls or expensive work inside the log statement, no logging in loops.
- **Correlation.** The class name is the default key; where a request/stream identity exists, prefix it (`[{correlationId}] …`), as stream-publisher/subscriber already do.
- **Never** use `System.out` / `System.err` in production code.

## Design decisions & alternatives discarded

|                       Decision                        |                                                    Rationale                                                    |                                                 Alternatives discarded                                                 |
|-------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| `System.Logger` + JUL backend                         | JDK-native, zero extra dependency, clean under JPMS, OTEL-compatible facade; other frameworks bridge *into* it. | Log4j2 / Logback as primary (extra deps, JPMS friction, larger CVE surface); SLF4J as primary (still needs a backend). |
| Single-line text format                               | `tail`/`grep`-friendly and ingested as-is by Loki/ELK/Splunk; cheap.                                            | Structured/JSON logs (poorly supported by tail-style readers; heavier — explicitly discouraged in the guidelines).     |
| Two formatters (colour local / plain prod)            | Developer ergonomics locally; plain, parseable text in prod.                                                    | One formatter everywhere (either no colour locally or ANSI noise in prod files).                                       |
| JUL `.properties` via `java.util.logging.config.file` | Native per-class/package granularity from a single source of truth; injected identically by Docker and Helm.    | A coarse `LOG_LEVEL` env var (global only — cannot target one class during an incident).                               |
| Metrics for progress; success never INFO              | Keeps production INFO signal-dense and low-volume; progression is a time-series concern.                        | Promoting per-block milestones to INFO (per-block spam; buries real problems).                                         |
| One periodic INFO heartbeat                           | Gives log-only operators a continuous progress/liveness signal without per-item logs.                           | No heartbeat (log-only operators are blind to progress); per-block INFO (spam).                                        |

## Newcomer checklist

1. Get the logger with `System.getLogger(getClass().getName())`.
2. Pick the level from the table above — if in doubt, it is probably DEBUG. Success is not INFO.
3. Use `{n}` placeholders; pass the exception as the trailing argument.
4. Ask: *would an operator page on this?* → WARNING/ERROR. *Is it a rate or count?* → a metric, not a log.
5. Keep it to one short line; never log inside a loop.

## Open questions / future work

- Native OTEL log export (currently only metrics are OTEL-exported; logs ship as text via Alloy/Loki).
- Metric gaps behind demoted logs (e.g. subscriber lag, handler churn) — file follow-ups so the demotions do not lose signal.
- Consolidating the logging config/formatter copies duplicated into the simulator.
