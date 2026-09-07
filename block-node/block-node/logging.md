# Block Node Logging Reference

This page is the operator and developer reference for **reading and configuring** Block Node
logs: the format, how to change the log level (globally, per package, or per single class),
and when to reach for logs versus metrics.

- For the **level definitions** engineers apply when writing code, see [Logging Guidelines](../logging-guidelines.md).
- For the **why** (framework, trade-offs, design decisions), see [Logging Architecture](../design/architecture/Logging-Architecture.md).
- For **incident runbooks and the log playbook**, see [Troubleshooting](./troubleshooting.md).

## Table of contents

1. [Log format](#log-format)
2. [Logs vs. metrics: which one do I use?](#logs-vs-metrics-which-one-do-i-use)
3. [Log levels](#log-levels)
4. [Changing the log level](#changing-the-log-level)
   - [Per single class](#per-single-class)
   - [Per package](#per-package)
   - [Globally](#globally)
   - [Local / jar](#local--jar-runs)
   - [Docker](#docker)
   - [Kubernetes / Helm](#kubernetes--helm)
5. [Enabling DEBUG on demand (log-only troubleshooting)](#enabling-debug-on-demand-log-only-troubleshooting)

## Log format

Logs are **single-line text** written to stdout/stderr (and, in container deployments, to a
rotating file), so they ingest cleanly into Loki, ELK, Splunk, etc.

- **Production (Docker / Kubernetes)** uses `java.util.logging.SimpleFormatter`:

  ```text
  2026-02-25 21:07:36.281+0000 INFO    [org.hiero.block.node.app.BlockNodeApp start] Started BlockNode Server : State=RUNNING HistoricBlockRange=[0, 12345]
  ```

  Fields: `<date> <time.millis><tz> <LEVEL> [<class> <method>] <message>`.

- **Local development** uses `CleanColorfulFormatter` — the same fields, ANSI-coloured by
  level (ERROR red, WARNING yellow, INFO white, DEBUG/TRACE cyan), with the class shortened to
  its simple name.

In production the rotating log file is written to `/opt/hiero/block-node/logs/blocknode-%g.log`
(50 MB per file, 15 files retained ≈ 750 MB of history). In Kubernetes this lives on a dedicated
`logging` volume.

## Logs vs. metrics: which one do I use?

The Block Node follows the standard split — **metrics for progress, logs for problems**:

|               You want to know…                |                       Use                       |                                      Example                                       |
|------------------------------------------------|-------------------------------------------------|------------------------------------------------------------------------------------|
| How many / how fast / how healthy over time    | **Metrics** ([metrics reference](./metrics.md)) | block height, `blocknode_verification_blocks_failed`, receive latency, queue depth |
| What specific, notable thing happened, and why | **Logs**                                        | a verification rejection, an I/O failure, a plugin failing to start                |

Per-block success and throughput are **metrics**, not per-block INFO logs. Production enables
INFO and above, and INFO is deliberately low-volume so that a WARNING or ERROR stands out. To
watch block progression continuously at INFO, use the periodic status **heartbeat** line; for
block-by-block detail, [enable DEBUG on demand](#enabling-debug-on-demand-log-only-troubleshooting).

## Log levels

The code uses `System.Logger` levels; the log configuration file uses the equivalent
`java.util.logging` (JUL) names:

| `System.Logger` | JUL name (use this in `logging.properties`) | Enabled in production? |
|-----------------|---------------------------------------------|------------------------|
| ERROR           | `SEVERE`                                    | yes                    |
| WARNING         | `WARNING`                                   | yes                    |
| INFO            | `INFO`                                      | **yes (default)**      |
| DEBUG           | `FINE`                                      | no (enable on demand)  |
| TRACE           | `FINEST`                                    | no                     |

Verbosity order, most to least: `ALL FINEST FINER FINE CONFIG INFO WARNING SEVERE OFF`.
Setting a level enables that level **and everything less verbose**. Example: `FINE` shows
`FINE`, `INFO`, `WARNING`, `SEVERE`.

## Changing the log level

Levels are controlled entirely through a JUL `logging.properties` file — there is **no
`LOG_LEVEL` environment variable**. The three scopes below work identically in every
environment; only *which file you edit* differs.

### Per single class

Use the fully-qualified class name plus `.level`. This is the most surgical control — raise
one noisy or interesting class without touching anything else:

```properties
# Verbose logging for just the verification plugin class
org.hiero.block.node.block.verification.VerificationServicePlugin.level = FINE

# Trace a single storage class
org.hiero.block.node.blocks.files.recent.BlockFileRecentPlugin.level = FINEST
```

### Per package

Drop the class name to target a whole package subtree:

```properties
# DEBUG for everything under the verification module
org.hiero.block.node.block.verification.level = FINE

# DEBUG for all Block Node code (the whole org.hiero.block package tree)
org.hiero.block.level = FINE
```

### Globally

The root `.level` sets the default for anything without a more specific rule:

```properties
# Make the whole application (including libraries) DEBUG — very verbose, avoid in prod
.level = FINE

# Quiet a chatty third-party package back down while the root stays DEBUG
com.sun.net.httpserver.level = WARNING
```

More specific rules always win over less specific ones, so you can combine them — e.g. keep
`.level = INFO` globally but set `org.hiero.block.node.backfill.level = FINE` to debug only
backfill.

### Local / jar runs

Edit the bundled file and rerun:

```properties
# block-node/app/src/main/resources/logging.properties
.level = INFO
org.hiero.block.node.block.verification.level = FINE
```

Or point the JVM at any file without rebuilding:

```bash
java -Djava.util.logging.config.file=/path/to/my-logging.properties \
     -p app.jar -m org.hiero.block.node.app/org.hiero.block.node.app.BlockNodeApp
```

### Docker

Edit the mounted config file and restart the container:

```properties
# block-node/app/docker/logging.properties  (mounted at
# /opt/hiero/block-node/logs/config/logging.properties)
.level = INFO
org.hiero.block.node.block.verification.VerificationServicePlugin.level = FINE
```

The container already sets `-Djava.util.logging.config.file` to that path via
`JAVA_TOOL_OPTIONS`, so the file is picked up on restart.

### Kubernetes / Helm

Set the global level and any per-package/class rules in your Helm values; they are rendered
into the `logging.properties` ConfigMap and mounted into the pod:

```yaml
# charts/block-node-server/values.yaml (or your overrides file)
blockNode:
  logs:
    level: "INFO"                         # global default (.level)
    loggingProperties:
      org.hiero.block.level: "FINE"       # per-package
      org.hiero.block.node.block.verification.VerificationServicePlugin.level: "FINE"  # per-class
      com.sun.net.httpserver.level: "WARNING"
```

Apply with `helm upgrade …` (or `kubectl apply` the rendered ConfigMap) and restart the
StatefulSet pods so the new configuration is loaded.

## Enabling DEBUG on demand (log-only troubleshooting)

If you can only see logs (no metrics/Grafana) and need block-by-block detail during an
incident, raise **just the relevant package** to `FINE`, observe, then revert — rather than
enabling DEBUG globally:

1. Add a targeted rule (pick the subsystem you are investigating):

   ```properties
   org.hiero.block.node.block.verification.level = FINE   # verification detail
   # or
   org.hiero.block.node.backfill.level = FINE       # backfill gap/scheduling detail
   ```
2. Restart / reload so the change takes effect, and reproduce the issue.
3. **Revert** the rule once done — DEBUG is high-volume and not meant to run continuously in
   production.

See the [troubleshooting playbook](./troubleshooting.md) for which subsystem to raise for a
given symptom, and the healthy-node INFO signature to compare against.
