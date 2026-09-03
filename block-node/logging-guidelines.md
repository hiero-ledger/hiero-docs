# Logging Guidelines for Block Node

## Table of Contents

1. [Purpose](#purpose)
2. [Level Definitions](#level-definitions)
3. [Recommended Usage](#recommended-usage)
4. [Things to Avoid](#things-to-avoid)

## Purpose

This document seeks to provide a common baseline of understanding for all
engineers working on the Block Node project. Herein we provide an agreed
definition for each logging level, recommendations for using log statements,
and some common anti-patterns that should be avoided.

## Level Definitions

<dl>
  <dt>ERROR</dt>
  <dd>Events that indicate the system is entering a failure state and
    requires immediate attention.<br/>
    This level almost always triggers a critical alert to the entire operations
    team, and should be considered only for situations that require immediate
    attention to prevent catastrophic results.<br/>
    Good examples might include detecting a condition that will result in loss
    of the entire local archive, or detection of a _high probability_ that the
    system has suffered a substantial cybersecurity breach.<br/>
    <p style='margin-left:2em;margin-top:0em'>Example:<br/>
        In the "Recent Blocks" plugin, a disk full error might be logged at
        <code>ERROR</code> level, as that block node cannot continue its
        primary purpose when the disk is full.
    </p>
    </dd>
  <dt>WARNING</dt>
  <dd>Events that indicate the system is not healthy, but not immediately
    failing.<br/>
    This level almost always triggers a call to the on-call operations engineer
    and this should be considered whenever writing a log at this level.<br/>
    If the information presented, or triggering event, is not sufficient to
    warrant creating a trouble ticket and alerting the on-call staff, then
    that event is most likely a `DEBUG` or lower level.<br/>
    <p style='margin-left:2em;margin-top:0em'>Example:<br/>
        In the "Validation" plugin, a signature in the wrong format might be
        logged at <code>WARNING</code> level, as the misformatted signature
        is recoverable (the block just fails validation), but might be a signal
        of something more significant (either defect in the plugin or malicious
        activity).
    </p>
  </dd>
  <dt>INFO</dt>
  <dd>Information potentially useful when monitoring and managing the system.<br/>
    This level is often enabled in production environments, and use should be
    carefully considered and limited only to items that are unequivocally useful
    to an operations engineer in a production environment. This level should not
    be used for information that is primarily useful to a software development
    engineer or primarily relevant to testing environments.<br/>
    <p style='margin-left:2em;margin-top:0em'>Example:<br/>
        In the "State Management" plugin, each state snapshot event might be
        logged at <code>INFO</code> level. The information is reasonably useful
        to operations engineers who might need to track down written snapshots,
        is not specifically useful only when debugging, does not indicate a
        problem or potential problem, and is infrequent (perhaps once every
        15 minutes).<br/>
        It is perhaps clear from this that <code>INFO</code> is the level that
        has the most requirements to meet before it is used.
    </p>
  </dd>
  <dt>DEBUG</dt>
  <dd>Information potentially useful when debugging hard-to-find errors in
    test networks or when debugging unit tests.<br/>
    This level is typically detailed and should not be enabled in production
    deployments, but may be enabled in testing environments.<br/>
    In very rare circumstances this level might be enabled in a targeted
    manner for production to track down intermittent issues.<br/>
    <p style='margin-left:2em;margin-top:0em'>Example:<br/>
        In the "Archive" plugin the file name and bucket path for each tar
        file might be logged at <code>DEBUG</code> level when the file is
        opened and when it is closed. This is clearly only useful for debugging
        the plugin, but is not so voluminous as to be overwhelming when enabled
        globally in a local debugging environment.
    </p>
  </dd>
  <dt>TRACE</dt>
  <dd>Information used when debugging code or unit tests.<br/>
    This level is typically very detailed and should not be enabled in
    test or production deployments except when highly targeted and
    strongly warranted.<br/>
    <p style='margin-left:2em;margin-top:0em'>Example:<br/>
        In a "server status" plugin, each request might be logged at the
        <code>TRACE</code> level, as the volume in production is not extreme,
        but is still much too detailed to be enabled in production except in
        very rare circumstances.
    </p>
  </dd>
</dl>

## Recommended Usage

Writing log statements is a common and useful practice, but it is important to
use them judiciously.

* Use the appropriate level for the information being logged.
  * Use `ERROR` for events that require immediate attention.
  * Use `WARNING` for events that indicate the system is not healthy.
  * Use `INFO` for information useful to operations engineers in production
    environments.
  * Use `DEBUG` for information useful to software development engineers.
  * Use `TRACE` for very detailed information used when debugging code or unit
    tests.
    * Most `TRACE` level log statements should be removed before code is merged
      to the `main` branch.
* Make use of the parameterized log message features for messages that might
  be expensive to produce.
  * For example, in Java you could use `Message.Format` placeholders in the log
    message, and then pass the parameters to the logger.
  * It is also possible to use `Logger.isLoggable(Level)` to check if the log
    level is enabled before creating the message, but this is often an
    indication that the log statement is excessive or logging is being used for
    something other than operational diagnostics.
  * It is also possible to use `String.format` to format messages, but this is
    executed regardless if the statement will be logged, so use should be
    carefully considered. In some cases method handles can be used to ameliorate
    this concern.
* Include log statement usage in review criteria for pull requests, and actively
  consider if each log statement is necessary and useful, as well as if the log
  level is appropriate.
  * Be particularly careful with `INFO` level log statements; this level is the
    most frequently overused and often contains far more detail than is
    appropriate.

## Logs vs. Metrics

Logs and metrics answer different questions; choosing the right one keeps `INFO` useful in
production.

* Use **metrics** for anything you would count, rate, or trend over time — blocks received /
  verified / persisted, latencies, queue depth, connection counts. **Per-item success and
  throughput belong in metrics, never in per-item `INFO` logs.** Logging success is not an
  `INFO`-level concern.
* Use **logs** for discrete, notable events and problems — a verification rejection, an I/O
  failure, a plugin failing to start, a lifecycle transition.
* Operators who need continuous block-progression visibility from `INFO` alone use the periodic
  status **heartbeat** (a single low-frequency aggregate line emitted from the server-status
  layer), not per-block logs. For block-by-block detail, `DEBUG` is enabled on demand for the
  relevant package. See the [Logging Reference](block-node/logging.md).

## Block Node examples

Concrete forms of the guidance above, using `System.Logger` as this project does.

Correct — parameterized, deferred, exception passed as the trailing argument:

```java
LOGGER.log(INFO, "Started BlockNode Server : State={0} HistoricBlockRange={1}", state, range);
LOGGER.log(WARNING, "State-proof verification rejected block {0}", blockNumber);
LOGGER.log(ERROR, "Could not initialize historic plugin due to I/O exception", e);
```

Avoid — these defeat deferred evaluation or lose the stack trace:

```java
// Eager string building even when the level is disabled
LOGGER.log(DEBUG, "Wrote block " + blockNumber + " to " + path);
LOGGER.log(INFO, "Deleted %s".formatted(path));           // .formatted() with no exception

// Stack trace lost: message only, or exception used as a format argument
LOGGER.log(WARNING, "Failed to fetch from {0}: {1}", url, e.getMessage());  // no throwable
LOGGER.log(WARNING, "Unable to close client: {0}", e);    // e fills {0}, no stack trace
```

Also avoid emitting a *failure* at `INFO` (it becomes indistinguishable from routine chatter in
production) — a caught exception is almost always `WARNING` or `ERROR`, not `INFO`.

### One-time lifecycle events stay at INFO

The other side of keeping `INFO` low-volume: **infrequent, one-time lifecycle events belong at
`INFO`**, because they are exactly the operationally-significant, low-frequency signals an
operator needs in production. These include:

* Application **startup** and **shutdown** (the startup banner, "Started BlockNode Server …",
  "Shutting down, reason=…", "System Exiting").
* The effective-**configuration dump** at startup (with sensitive values masked).
* The **plugin load list** and **port binding** ("BlockNode Primary Server configured on
  port(s): …").
* Other genuinely one-time events (e.g. loading block ranges from a file on start).

These fire once per lifecycle, not per block or per request, so they do not create noise. Do
**not** demote them to `DEBUG`. Note the distinction from a *failure* during startup/shutdown
(for example, failing to close a scheduler): the failure is `WARNING`/`ERROR`, while the normal
"shutting down" / "started" milestone stays `INFO`. Recurring per-item work and per-plugin
internal init detail remain `DEBUG`; the app already lists each loaded plugin at `INFO`, so
operators see which plugins started without each plugin logging its own start at `INFO`.

## Things to Avoid

There are a number of common anti-patterns that should be avoided when writing
log statements.

* Avoid writing log statements in loops.
  * If you need to log a large amount of information, you are almost certainly
    misusing logs for some purpose other than operational diagnostics.
  * Consider why this information might be logged, and explore other options
    such as using a metrics library or transmitting the information to a
    dedicated storage system.
* Avoid log statements that require significant amounts of memory or processing.
  * Assume each log statement must fit on a single line of no more than
    approximately 200 characters.
    * This is primarily due to the use of very limited logging frameworks, such
      as Log4J, which transform the log data into a string very early. This
      prevents use of more efficient transmission to remote diagnostic
      management systems which could interpret detailed data effectively.
    * In such systems, log data is written to files, and multi-line events are
      poorly supported in "tail" style log readers.
  * Avoid including a method call or other complex operation in the log
    statement.
  * Consider using method _handles_ or lambda expressions to defer the creation
    of the log message if costly operations are truly necessary.
    * This is usually an indication that the log statement is excessive, but it
      can be useful in rare cases.
* Do not use `System.out` or `System.err` for any purpose in production code.
  * Anything that might be written using these classes is either unnecessary
    noise, or should be written using the logging framework.
