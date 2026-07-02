# New Plugin Architecture

## Table of Contents

1. [Purpose](#purpose)
2. [Goals](#goals)
3. [Terms](#terms)
4. [Entities](#entities)
5. [Design](#design)
6. [Diagram](#diagram)
7. [Configuration](#configuration)
8. [Metrics](#metrics)
9. [Exceptions](#exceptions)
10. [Acceptance Tests](#acceptance-tests)

## Purpose

The Block Node is composed of many independently-developed functional components: block publishing, subscribing, verification, persistence, health checking, cloud archival, roster bootstrapping, and more. Rather than wiring these together statically at compile time, the Block Node uses a **plugin architecture** that discovers, configures, and manages the lifecycle of each component at runtime.

This document describes a revised design of that architecture. The previous design ([current-plugin-architecture.md](current-plugin-architecture.md)) injected facilities into a shared `BlockNodeContext` record, which created an implicit coupling between the application bootstrap code and every facility type. This revision removes the concept of **facilities** — each functional component is implemented as a `BlockNodePlugin` and is discovered by the `ServiceLoader` via JPMS. Any plugin that needs to expose additional services outside of the `BlockNodePlugin` interface can expose its own typed interface as a JPMS service. Plugins that wish to use this additional interface can specify a dependency in their `module-info.java` file and can discover the dependent plugins via `ServiceLoader` in their own `init()` method. The application bootstrap code becomes facility-free. Dependency resolution and readiness coordination move into the plugins themselves.

## Goals

- Allow functional components to be added, replaced, or removed without modifying the application bootstrap code.
- Remove facilities from `BlockNodeContext` and the `BlockNodeApp`.
- Allow plugins to discover other plugins by their unique interface via `ServiceLoader`.
- Allow `init()` to execute in any order, or in parallel, with no assumptions about other plugins being initialized.
- Allow `start()` to coordinate readiness with plugins via `awaitReady()`, without the application imposing an ordering constraint.
- Ensure plugins that cannot satisfy a required dependency mark themselves as unhealthy rather than throwing, so the rest of the plugins can continue to start.
- Support typed, immutable configuration for each plugin using Java records and the Swirlds Config API.
- Remain entirely within the Java Platform Module System so that module boundaries are enforced by the JVM.

## Terms

<dl>
  <dt>Plugin</dt>
  <dd>Any class that implements <code>BlockNodePlugin</code> and is registered as a Java module service. Plugins are the unit of extension — each adds a distinct capability to the running Block Node.  Plugins can implement and expose additional interfaces registered as a JPMS service. Other plugins that depend on those interface can discover them via <code>ServiceLoader</code>.</dd>

  <dt>Facility</dt>
  <dd>The deprecated pattern for implementing specific functionality made available to plugins. (e.g., <code>BlockMessagingFacility</code>, <code>HistoricalBlockFacility</code>, <code>ApplicationStateFacility</code>)</dd>

  <dt>SPI</dt>
  <dd>Service Provider Interface — a Java interface declared in the <code>spi-plugins</code> module and consumed via <code>java.util.ServiceLoader</code>. Plugins provide implementations of SPI interfaces in their own modules.</dd>

  <dt>BlockNodeContext</dt>
  <dd>A slimmed-down immutable Java record passed to every plugin during <code>init()</code>. It carries only infrastructure concerns: <code>Configuration</code>, <code>MetricRegistry</code>, <code>ServiceLoaderFunction</code>, <code>ThreadPoolManager</code>. Facilities and plugin data are no longer fields on this record.</dd>

  <dt>ServiceBuilder</dt>
  <dd>An interface passed alongside <code>BlockNodeContext</code> during <code>init()</code> that allows plugins to register HTTP and gRPC service routes on the Helidon web server.</dd>

  <dt>Disruptor</dt>
  <dd>The LMAX Disruptor ring buffer used by the <code>BlockMessagingFacility</code> plugin as the underlying transport for both block-item distribution and block-notification events between plugins. This messaging provider may be changed at a future date.</dd>

  <dt>BlockProviderPlugin</dt>
  <dd>A plugin that also implements the BlockSource interface and registers that interface as a JPMS service. Plugins that require a BlockSource will add `uses org.hiero.block.node.spi.BlockSource` to their module info file. There can be many BlockSources. It is up to the plugin to decide how to sort/choose the BlockSources they need to use.

  <dt>awaitReady()</dt>
  <dd>A method on <code>BlockNodePlugin</code> that returns <code>true</code> when the plugin is fully initialized and ready to serve its callers. Plugins expose this so the `BlockNodeApp` and other plugins can inspect the readiness of any given plugin.</dd>
</dl>

## Entities

### `BlockNodePlugin` (interface)

The root SPI interface. Every plugin implements this interface. All methods have default no-op or default-value implementations so plugins only override what they need.

|             Method              |                  When called                  |                                                                                                                             Purpose                                                                                                                              |
|---------------------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name()`                        | Anytime                                       | Human-readable identifier; defaults to the simple class name.                                                                                                                                                                                                    |
| `version()`                     | After load                                    | Returns the plugin's version from its JAR manifest.                                                                                                                                                                                                              |
| `configDataTypes()`             | Before config load                            | Declares `@ConfigData`-annotated record classes the plugin needs. All types are collected from every plugin before configuration is built.                                                                                                                       |
| `init(context, serviceBuilder)` | During startup, any order                     | Plugin uses `ServiceLoader` to locate plugins it depends on. Logs an error and marks itself unhealthy if a required dependency is missing. Registers HTTP/gRPC routes. Must not start background threads. Must not assume any other plugin has been initialized. |
| `awaitReady()`                  | Anytime after `init()`                        | Returns `true` when the plugin is ready to serve callers. Defaults to `false`.                                                                                                                                                                                   |
| `start()`                       | After all `init()` calls, on a virtual thread | Loads data and resources. Waits on any required plugins via `awaitReady()` before proceeding. Starts background worker threads.                                                                                                                                  |
| `stop()`                        | During graceful shutdown                      | Closes resources, stops worker threads.                                                                                                                                                                                                                          |

#### `ServerStatusPlugin`

Owns and persists the mutable node state (`BlockNodeVersions`). These fields have been removed from `BlockNodeContext`. Plugins that need this state discover the `ServerStatusPlugin` via `ServiceLoader`.

### `BlockNodeContext` (record)

An immutable record injected into every plugin during `init()`. Contains only infrastructure concerns shared by all plugins. Facilities and plugin data are no longer fields here.

|        Field        |          Type           |                                       Description                                        |
|---------------------|-------------------------|------------------------------------------------------------------------------------------|
| `configuration`     | `Configuration`         | Swirlds typed configuration. Plugins call `configuration.getConfigData(MyConfig.class)`. |
| `metricRegistry`    | `MetricRegistry`        | Register counters, gauges, and histograms.                                               |
| `serviceLoader`     | `ServiceLoaderFunction` | Load plugins and other SPI extensions at runtime.                                        |
| `threadPoolManager` | `ThreadPoolManager`     | Create managed virtual-thread or platform-thread executors.                              |

### `ServiceBuilder` (interface)

Passed to plugins during `init()`. Allows plugins to register routes without a direct dependency on Helidon.

```java
void registerHttpService(String path, HttpService... service);
void registerGrpcService(ServiceInterface service);
```

### Plugins

A plugin implements `BlockNodePlugin`. It can also define its own interface in its own module. The `BlockNodePlugin` interface must be registered as a JPMS service in its module info file. Additional interfaces may be registered as a JPMS service.

#### `BlockMessagingFacility`

The inter-plugin event bus currently built using LMAX Disruptor ring buffers for high-throughput, low-latency delivery. This messaging provider may be changed at a future date.

**Block Items** — a stream of `BlockItems` records carrying unparsed protobuf items for one logical block:
- `sendBlockItems(BlockItems)` — called by the publisher plugin for every batch received from a Consensus Node.
- `registerBlockItemHandler(BlockItemHandler, cpuIntensive, name)` — subscribe with back-pressure; a slow handler slows the producer.
- `registerNoBackpressureBlockItemHandler(...)` — subscribe without back-pressure; if the handler falls 80% behind, `onTooFarBehindError()` is called instead of blocking the producer.

**Block Notifications** — five typed notification events for block lifecycle milestones:

|              Notification               |       Sender        |                          Meaning                           |
|-----------------------------------------|---------------------|------------------------------------------------------------|
| `VerificationNotification`              | Verification plugin | Block passed or failed cryptographic verification.         |
| `PersistedNotification`                 | Storage plugins     | Block has been durably written by a provider.              |
| `BackfilledBlockNotification`           | Backfill plugin     | A previously missing block was retrieved and stored.       |
| `NewestBlockKnownToNetworkNotification` | Publisher plugin    | Consensus Node has reported the latest known block header. |
| `PublisherStatusUpdateNotification`     | Publisher plugin    | Publisher connection state changed.                        |

Any plugin may subscribe to notifications via `registerBlockNotificationHandler(BlockNotificationHandler, ...)`.

#### `HealthFacility`

```java
enum State { STARTING, RUNNING, SHUTTING_DOWN }
State blockNodeState();
void shutdown(String className, String reason);
```

The `HealthServicePlugin` registers `/healthz/livez` and `/healthz/readyz` HTTP endpoints that return `200 OK` while the state is `RUNNING` and `503 Service Unavailable` otherwise.

The Health Facility has responsibility for the following:

1. Receive updates from the core App and all plugins regarding current health status.
2. Determine, based on plugin-specific criteria, whether the full node is ready, live, and/or offline based on all available information.
3. Provide data to the status plugin for exposure on the status API
4. Provide the K8S healthz endpoints.
5. Provide a statusz endpoint for monitoring tools that offers a detailed view of node health status.

#### `ApplicationStateFacility`

Owns and persists the mutable node state (`TssData`, `NodeAddressBook`). These fields have been removed from `BlockNodeContext`. Plugins that need this state discover `ApplicationStateFacility` via `ServiceLoader` and register change listeners directly.

```java
// State access
TssData tssData();
NodeAddressBook nodeAddressBook();

// State mutation (persists to disk and notifies listeners)
void updateTssData(TssData tssData);
boolean updateAddressBook(NodeAddressBook nodeAddressBook);

// Change listener registration
void registerTssDataListener(Consumer<TssData> listener);
void registerAddressBookListener(Consumer<NodeAddressBook> listener);
```

Plugins register listeners in `init()` so they receive updates as soon as the plugin is discovered. The plugin delivers the current value immediately upon listener registration if state is ready, and delivers each subsequent update as it occurs.

### `BlockProviderPlugin` (interface)

Specialization of `BlockNodePlugin` for block storage backends. Each provider declares a `defaultPriority()` (higher = preferred).

## Design

### Plugin Pattern

Every plugin follows the same pattern:

1. **Create** a new module for the Plugin
2. **Implement** `BlockNodePlugin`.
3. **Implement** interface(s) for any additional services provided by this plugin. (OPTIONAL)
4. **Register** both `BlockNodePlugin` and any additional interfaces as JPMS services in `module-info.java`.
5. **Override** `awaitReady()` to return `true` only after internal startup is complete.

Dependent plugins declare `uses <AdditionalInterface>` in their own `module-info.java` and resolve the required plugin in `init()`:

```java
// stream-publisher/module-info.java
uses org.hiero.block.node.spi.blockmessaging.BlockMessagingFacility;

// PublisherPlugin.java
@Override
public void init(BlockNodeContext context, ServiceBuilder serviceBuilder) {
    messaging = context.serviceLoader()
        .loadServices(BlockMessagingFacility.class)
        .findFirst()
        .orElse(null);
    if (messaging == null) {
        log.log(ERROR, "Required BlockMessagingFacility not found — publisher marked unhealthy");
        markUnhealthy();
    }
}

@Override
public void start() {
    // Wait for the plugin to be ready before starting workers
    messaging.awaitReady();
    startWorkerThreads();
}
```

### Plugin Discovery and Bootstrap Sequence

```
1. BlockNodeApp loads all BlockNodePlugin implementations via ServiceLoader.

2. Collect configDataTypes() from every plugin + built-in config types.

3. Load Configuration from sources (classpath properties, environment variables,
   system properties) using the collected record types.

4. Construct BlockNodeContext with infrastructure fields only
   (configuration, metricRegistry, serviceLoader, threadPoolManager).

5. Call plugin.init(context, serviceBuilder) for every plugin.
   - Order is not guaranteed; init() calls may run in parallel.
   - Each plugin uses context.serviceLoader() to discover any plugins it needs.
   - Missing required plugins are logged and the plugin marks itself unhealthy.
   - Plugins register HTTP/gRPC routes via ServiceBuilder.
   - Plugins register state-change listeners with ApplicationStateFacility.

6. Start Helidon WebServer using routes accumulated in ServiceBuilder.

7. Call plugin.start() for every plugin, each on its own virtual thread.
   - start() may call awaitReady() on plugins to block until the plugin is ready.
   - Plugins reach awaitReady() == true once their own internal startup completes.
   - Plugins start their workers once dependencies are ready.

8. Node is RUNNING once all plugins have started (or been marked unhealthy).

9. State changes (TssData, NodeAddressBook) are pushed directly to registered
   listeners by ApplicationStateFacility. No onContextUpdate() broadcast occurs.

10. On shutdown signal:
    a. Transition to SHUTTING_DOWN.
    b. Wait for configurable shutdown delay.
    c. Stop WebServer.
    d. Call plugin.stop() for every plugin.
    e. Close MetricRegistry.
    f. Exit JVM.
```

### Module System Integration

Every module declares its service registrations in `module-info.java`. Plugin modules register both `BlockNodePlugin` (so the app discovers them) and any additional interfaces (so dependent plugins can discover them directly).

```java
// spi-plugins module-info.java
uses org.hiero.block.node.spi.BlockNodePlugin;
uses org.hiero.block.node.spi.blockmessaging.BlockMessagingFacility;
uses org.hiero.block.node.spi.historicalblocks.HistoricalBlockFacility;
uses org.hiero.block.node.spi.historicalblocks.BlockProviderPlugin;
uses org.hiero.block.node.spi.health.HealthFacility;
uses org.hiero.block.node.spi.state.ApplicationStateFacility;

// facility-messaging module-info.java
provides org.hiero.block.node.spi.BlockNodePlugin
    with BlockMessagingFacilityImpl;
provides org.hiero.block.node.spi.blockmessaging.BlockMessagingFacility
    with BlockMessagingFacilityImpl;

// stream-publisher module-info.java
uses org.hiero.block.node.spi.blockmessaging.BlockMessagingFacility;
provides org.hiero.block.node.spi.BlockNodePlugin
    with PublisherPlugin;

// roster-bootstrap-tss module-info.java
uses org.hiero.block.node.spi.state.ApplicationStateFacility;
provides org.hiero.block.node.spi.BlockNodePlugin
    with TssBootstrapPlugin;
```

Adding a new plugin requires only:
1. Implementing `BlockNodePlugin` (or a specialization).
2. Declaring `provides org.hiero.block.node.spi.BlockNodePlugin with YourPlugin` in `module-info.java`.
3. Declaring `uses <AdditionalInterface>` for each plugin it depends on.
4. Adding the module to the Gradle build.

No changes to `BlockNodeApp` or any existing plugin are required.

### `init()` Ordering and Parallelism

Because `init()` may run in any order or in parallel, it must be entirely self-contained:

- It must not call methods on other plugins directly.
- It must not assume that any plugin's `awaitReady()` returns `true`.
- It **may** call `context.serviceLoader().loadServices(FacilityInterface.class)` to obtain a reference to a plugin it depends on (the plugin object exists even if not yet ready).
- It **may** call `registerTssDataListener(...)` on `ApplicationStateFacility` — listener registration is safe before the plugin is ready; the first delivery occurs once ready.
- It **must** log an error and mark itself unhealthy if a required plugin is not found.

### `start()` Readiness Coordination

`start()` runs on a virtual thread spawned by the application. Because virtual threads are cheap, a plugin may call `awaitReady()` on another plugin without consuming a platform thread:

```java
@Override
public void start() {
    // Block this virtual thread until the messaging plugin is ready.
    // Virtual thread parking is cheap — no platform thread is consumed.
    messaging.awaitReady();
    // Now safe to use the plugin.
    messaging.registerBlockItemHandler(this, false, name());
    startWorkerThread();
}
```

The `awaitReady()` contract for plugins:
- Returns `false` if it could not start or is unhealthy.
- Returns `true` if it started successfully.
- Is safe to call from any thread at any time.

### Mutable State Propagation via `ApplicationStateFacility`

`TssData` and `NodeAddressBook` are owned by the `ApplicationStateFacility` plugin. Interested plugins register listeners in `init()`. State updates flow as follows:

```
Bootstrap Plugin
    │
    ▼ applicationState.updateTssData(newData)
ApplicationStateFacilityImpl
    │
    ├── Persist to disk (JSON)
    ├── Store in memory (volatile reference)
    │
    ▼ notify registered listeners
    │
    ├── TssBootstrapPlugin.onTssDataChanged(newData)
    ├── VerificationPlugin.onTssDataChanged(newData)
    └── ... any other registered listener
```

This replaces the previous `onContextUpdate()` broadcast and the reconstruction of `BlockNodeContext`. Each listener is called directly and handles only the state it cares about.

### Inter-Plugin Messaging

Unchanged from the previous architecture. The `BlockMessagingFacility` plugin provides two independent LMAX Disruptor ring buffers:

```
Publisher Plugin
      │
      ▼  sendBlockItems()
┌─────────────────────┐
│  Block Items Buffer  │  (Disruptor ring)
└─────────────────────┘
      │           │
      ▼           ▼
 Subscriber    Verification    ...  (N handlers, each with optional back-pressure)

Block Lifecycle Events (verification, persistence, backfill, etc.)
      │
      ▼  sendBlockVerification() / sendBlockPersisted() / ...
┌────────────────────────────┐
│  Block Notification Buffer  │  (Disruptor ring)
└────────────────────────────┘
      │           │
      ▼           ▼
  Subscriber   Publisher   ...  (N handlers)
```

Handlers registered with back-pressure (default) slow the producer if they fall behind. Handlers registered without back-pressure (`NoBackPressureBlockItemHandler`) receive `onTooFarBehindError()` when they fall more than 80% of the ring size behind.

### Typed Configuration

Unchanged from the previous architecture. Each plugin declares the `@ConfigData`-annotated record classes it needs in `configDataTypes()`. All types from all plugins are aggregated before configuration is loaded.

```java
// Declaring config needs
@Override
public List<Class<? extends Record>> configDataTypes() {
    return List.of(PublisherConfig.class);
}

// Using config in init()
@Override
public void init(BlockNodeContext context, ServiceBuilder serviceBuilder) {
    PublisherConfig config = context.configuration().getConfigData(PublisherConfig.class);
}
```

Configuration sources are applied in ascending priority order:
1. Classpath (`application.properties`)
2. Environment variables (auto-mapped: `BLOCK_NODE_PUBLISHER_MAX_CONNECTIONS` → `publisher.maxConnections`)
3. System properties

## Diagram

### Plugin Lifecycle

```mermaid
stateDiagram-v2
    [*] --> Discovered : ServiceLoader.load()
    Discovered --> Configured : configDataTypes() collected
    Configured --> Initialized : init(context, serviceBuilder)
    Initialized --> NotHealthy : awaitReady() == false
    Initialized --> Ready : awaitReady() == true (no async startup)
    NotReady --> Ready : internal startup complete
    Ready --> Running : start() completes
    Running --> Stopped : stop()
    Stopped --> [*]

    note right of NotReady : Plugins spend time here\nloading state, building indexes, etc.
    note right of Initialized : A plugin marks itself Unhealthy\nif a required dependency is missing
```

### Startup Sequence

```mermaid
sequenceDiagram
    participant App as BlockNodeApp
    participant SL as ServiceLoader
    participant P as Plugin
    participant WS as Helidon WebServer

    App->>SL: load(BlockNodePlugin)
    SL-->>App: [plugin1, ..., pluginN]
    loop each plugin (any order)
        App->>P: configDataTypes()
        P-->>App: [ConfigRecord.class, ...]
    end
    App->>App: build Configuration
    App->>App: construct BlockNodeContext (no facilities)
    par init() — any order, may be parallel
        App->>P: init(context, serviceBuilder)
        P->>P: awaitReady() = false
        P->>SL: loadServices(FacilityInterface.class)
        SL-->>P: plugin reference
        P->>P: store reference, register listeners
        P->>WS: registerHttpService / registerGrpcService
    end
    App->>WS: start()
    par start() — each on its own virtual thread
        App->>P: start()
        P->>P: load state, build indexes
        App->>P: start()
        P->>P: awaitReady()? (blocks until true)
        P-->>P: true
        P->>P: start workers
    end
    Note over App,WS: Node is RUNNING
```

### Plugin Module Registration

```mermaid
flowchart TD
    SPI[spi-plugins\n<i>declares uses</i>]

    MSGF[facility-messaging\n<i>provides BlockNodePlugin</i>\n<i>provides BlockMessagingFacility</i>]
    HISTF[blocks-file-recent / historic / s3-archive\n<i>provides BlockNodePlugin</i>\n<i>provides BlockProviderPlugin</i>]
    HEALTHF[health\n<i>provides BlockNodePlugin</i>\n<i>provides HealthFacility</i>]
    STATEF[app-state\n<i>provides BlockNodePlugin</i>\n<i>provides ApplicationStateFacility</i>]

    PUB[stream-publisher\n<i>provides BlockNodePlugin</i>\n<i>uses BlockMessagingFacility</i>]
    SUB[stream-subscriber\n<i>provides BlockNodePlugin</i>\n<i>uses BlockMessagingFacility</i>\n<i>uses HistoricalBlockFacility</i>]
    VER[block-verification\n<i>provides BlockNodePlugin</i>\n<i>uses BlockMessagingFacility</i>\n<i>uses ApplicationStateFacility</i>]
    TSS[roster-bootstrap-tss\n<i>provides BlockNodePlugin</i>\n<i>uses ApplicationStateFacility</i>]

    MSGF -->|provides| SPI
    HISTF -->|provides| SPI
    HEALTHF -->|provides| SPI
    STATEF -->|provides| SPI
    PUB -->|uses| SPI
    SUB -->|uses| SPI
    VER -->|uses| SPI
    TSS -->|uses| SPI
```

### State Change Listener Flow

```mermaid
sequenceDiagram
    participant B as Bootstrap Plugin
    participant ASF as ApplicationStateFacility
    participant V as Verification Plugin
    participant T as TSS Plugin

    Note over V,T: Listeners registered during init()
    V->>ASF: registerTssDataListener(this::onTssDataChanged)
    T->>ASF: registerTssDataListener(this::onTssDataChanged)

    B->>ASF: updateTssData(newTssData)
    ASF->>ASF: persist to disk
    ASF->>V: onTssDataChanged(newTssData)
    ASF->>T: onTssDataChanged(newTssData)
```

## Configuration

The plugin architecture itself has no configuration. Each plugin declares its own configuration needs. The global sources and priorities are:

|                Source                | Priority |                  Example                  |
|--------------------------------------|----------|-------------------------------------------|
| System properties                    | Highest  | `-Dpublisher.maxConnections=10`           |
| Environment variables                | Middle   | `BLOCK_NODE_PUBLISHER_MAX_CONNECTIONS=10` |
| `application.properties` (classpath) | Lowest   | `publisher.maxConnections=10`             |

Environment variable mapping: `BLOCK_NODE_` prefix is stripped, remaining text is lowercased with `_` converted to `.` to match property key format.

## Metrics

No metrics are defined by the plugin framework itself. Each plugin is expected to register its metrics in `init()` using `context.metricRegistry()`. The standard category name for all block node metrics is defined as a constant on `BlockNodePlugin`:

```java
String METRICS_CATEGORY = "blocknode";
```

The `ApplicationStateFacility` plugin manages stored and available blocks. It registers two observable gauges:
- `blocknode.oldestBlockNumber` — oldest block available across all providers.
- `blocknode.newestBlockNumber` — newest block available across all providers.

## Exceptions

|                       Condition                        |                                                                        Behavior                                                                        |
|--------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| A required plugin interface has no registered provider | The dependent plugin logs an error in `init()`, marks itself unhealthy, and does not start. Other plugins are unaffected.                              |
| A plugin's `init()` throws an unchecked exception      | The exception is caught by the app; the plugin is marked unhealthy and skipped during `start()`. Other plugins continue.                               |
| A plugin's `start()` throws                            | The exception is logged; the plugin is considered failed. Other plugins on their own virtual threads are not affected.                                 |
| A plugin's `stop()` throws                             | The exception is logged; remaining plugins are still stopped.                                                                                          |
| A plugin's `awaitReady()` never returns `true`         | A dependent plugin's `start()` spins indefinitely. A startup watchdog timeout (configurable) should be implemented to detect and abort this condition. |
| A `NoBackPressureBlockItemHandler` falls 80% behind    | `onTooFarBehindError()` is called on that handler; the publisher is not blocked.                                                                       |
| A back-pressure `BlockItemHandler` falls behind        | The publisher thread blocks until the handler catches up, applying flow control end-to-end.                                                            |
| `ApplicationStateFacility.updateAddressBook()` fails   | Returns `false`; the caller is responsible for retry logic.                                                                                            |

## Acceptance Tests

- **Plugin discovery**: Starting the node with N plugins registered in `module-info.java` results in exactly N plugins having their `init()`, `start()`, and `stop() called, as confirmed by log output or a version-listing endpoint.
- **Plugin discovery by dependent**: A dependent plugin that calls `context.serviceLoader().loadServices(BlockMessagingFacility.class)` in `init()` receives the same object instance as the one loaded as a `BlockNodePlugin`.
- **init() order independence**: Calling `init()` on all plugins in reverse alphabetical order produces the same end state as calling them in forward alphabetical order. No plugin fails due to ordering.
- **Missing dependency handling**: Removing a required plugin's JAR from the module path causes the dependent plugin to log an error and mark itself unhealthy, while all other plugins start normally.
- **awaitReady() gate**: A dependent plugin's `start()` does not proceed past the readiness check until the plugin's `awaitReady()` returns `true`. Verified by injecting an artificial delay in a plugin's `start()` and observing the dependent plugin waits.
- **ApplicationStateFacility listener — initial delivery**: A plugin that registers a `TssDataListener` after the `ApplicationStateFacility` is ready receives the current `TssData` immediately upon registration.
- **ApplicationStateFacility listener — update delivery**: Calling `applicationStateFacility.updateTssData(newData)` results in every registered listener being called with `newData`. No `onContextUpdate()` broadcast occurs; `BlockNodeContext` is not reconstructed.
- **ApplicationStateFacility listener — NodeAddressBook**: Calling `applicationStateFacility.updateAddressBook(newBook)` delivers `newBook` to all registered `AddressBookListener` instances.
- **Plugin isolation**: Removing a plugin's JAR from the module path causes the node to start without that plugin's functionality, without failures in unrelated plugins.
- **Back-pressure**: A block-item handler that sleeps for 100 ms per batch reduces the observed throughput of `sendBlockItems()` to match, without deadlock or data loss.
- **No-back-pressure skip**: A `NoBackPressureBlockItemHandler` that processes slowly receives `onTooFarBehindError()` calls under sustained load without causing the publisher to block.
- **Configuration scoping**: A plugin's `@ConfigData` record is populated from its declared property keys; unrelated keys from other plugins do not affect its values.
- **Graceful shutdown**: `stop()` is called on every plugin, the web server closes, and the JVM exits with code 0.
- **HTTP route registration**: A plugin that calls `serviceBuilder.registerHttpService("/foo", ...)` in `init()` has its handler reachable at `GET /foo` once `start()` completes.
- **gRPC route registration**: A plugin that calls `serviceBuilder.registerGrpcService(...)` has its service reachable via HTTP/2 gRPC once `start()` completes.
- **Block provider priority**: When two `BlockProviderPlugin` implementations both have a block at number N, the provider with the higher `defaultPriority()` value is the one whose `BlockAccessor` is returned by `HistoricalBlockFacility.block(N)`.
