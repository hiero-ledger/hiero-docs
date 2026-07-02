# TSS Bootstrap Plugin Design

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

We intend to create a new Block Node plugin (TSSBootstrapPlugin) that queries another Block Node's detailed status to pick up TSS configuration details needed to initiate TSS verification.

## Goals

- BN allows TssData to be set via configuration
- BN persists the latest TssData to a configured file path
- BN loads persisted TssData at startup
- BN periodically contacts peer BNs to get their TssData
- BN makes TssData available via the ServerStatusDetail API
- BlockNodeApp provides an interface for BN Plugins to update TssData
- BN uses TssData.validFromBlock to determine which TssData is the latest
- BN updates plugins, on a separate thread, via onContextUpdate when the BlockNodeContext is updated

## Terms

<dl>
  <dt>TSS</dt>
  <dd>Threshold Signing Scheme, which consists of two main algorithms: WRAPS and hinTS.</dd>
</dl>
<dl>
  <dt>WRAPS (Weighted Roster Attestation Proof System)</dt>
  <dd>Proves validity of the hinTS verification key  of the network with a given Ledger ID.</dd>
</dl>
<dl>
  <dt>hinTS (hinted Threshold Signature scheme)</dt>
  <dd>Produces a hinTS signature attesting to the claim that majority of node by weight attest to block root hash.
      This hinTS signature is verified using a daily hinTS verification key.</dd>
</dl>
<dl>
  <dt>Verification Key</dt>
  <dd>The WRAPS verification key used to verify the WRAPS proof, with respect to a given ledger ID. This key is about 1.7KB.</dd>
</dl>
<dl>
  <dt>Ledger ID</dt>
  <dd>SHA-256 hash of the genesis TSS Address Book. This is 32 bytes.</dd>
</dl>
<dl>
  <dt>Schnorr public / private key</dt>
  <dd>A key produced privately by each node and used during the address book update process to adopt a new TSS roster.
      This key may also directly sign a block proof if the WRAPS process is not enabled.</dd>
</dl>

## Entities

### ApplicationStataFacility

- Responsible for updating the plugins when context changes.
- Calls the `BlockNodePlugin.onContextUpdate()`, on a separate thread, for all plugins when the `BlockNodeContext` changes.
- Passed directly to the plugins as a member of the `BlockNodeContext`.
- Plugins can use the `updateTssData` method to update the `TssData` in the `BlockNodeContext`
- Processes requests to change `TssData`
- Persists `TssData`
- Loads persisted `TssData` prior to plugin initialization
- Implemented by the `BlockNodeApp`

### BlockNodeContext

- Contains `TssData` information and is passed to plugins in a `BlockNodePlugin.init()` call.
- Contains the ApplicationStateFacility used by plugins to update `TssData`.
- The `BlockNodeContext` will also be sent to plugins via a `BlockNodePlugin.onContextUpdate()` call when the context changes.

### ServiceStatusServicePlugin

- The plugin responsible for the `serverStatus`  and `serverStatusDetail` gRPC calls.
- Implements the `BlockNodePlugin.onContextUpdate()` to receive `TssData` updates.
- Responds to peer requests for `TssData` via the `serverStatusDetail` call.

### TssBootstrapConfig

- Used to configure the BN Peers that will be used to query `serverStatusDetail`
- Used to bootstrap TSS data via the Config
- Overrides initial data provided by the `ApplicationStateFacility`, if it has a newer validFromBlock than
  other sources.

### TssBootstrapPlugin

- Queries peer BNs for `TssData` via the `serverStatusDetail` gRPC call.
- `TssData` can be configured using the `TssBootstrapConfig`
- `TssBootstrapConfig` data will override the `TssData` received during `init()`, if it has a newer validFromBlock than
  other sources.
- Notifies the `ApplicationStateFacility` when it has a `TssData` update.

### VerificationPlugin

- Verifies block 0
- Notifies the `ApplicationStateFacility` when it detects changes to `TssData`
- Implements the `BlockNodePlugin.onContextUpdate()` to receive `TssData` updates

### TssData

- The protobuf `TssData` used in the `serverStatusDetailsResponse`

```markdown
/**
 * TSS information<br/>
 * `TssData` contains the Ledger Id, wraps validation key, and TssRoster information
 *  It is returned by the serverStatusDetail service
 *
 * All fields in this message SHALL be filled in, or none.
 */
message TssData {
    /**
     * The ledger id
     */
    bytes ledger_id = 1;

    /**
     * The wraps verification key
     */
    bytes wraps_verification_key = 2;

    /**
     * The current TSS roster
     */
    TssRoster current_roster = 3;

    /**
     * The starting block number this TssData is valid from
     */
    uint64 valid_from_block = 4;
}

/**
 * TSS Roster information.
 *
 * `TssRoster` contains a list of roster entry
 */
message TssRoster {
    /**
     * The list of `RosterEntry`
     */
    repeated RosterEntry roster_entries = 1;

    /**
     * The starting block number this TssData is valid from
     */
    uint64 valid_from_block = 2;
}

/**
 * A single TSS Roster entry.
 *
 * All fields are REQUIRED.<br/>
 * The `node_id` field SHALL match the same field for an entry in the Node Store
 * in consensus network state.<br/>
 * The `schnorr_public_key` MAY be a placeholder value if the associated node failed
 * to participate in the roster election within the required time limit.
 */
message RosterEntry {
    /**
     * The node id
     */
    uint64 node_id = 1;

    /**
     * The node weight
     */
    uint64 weight = 2;

    /**
     * The schnorr public key
     */
    bytes schnorr_public_key = 3;
}
```

## Design

- Delivers TSS booststrap information to a Block Node that does not have access to the network Block 0 transactions.
  - A new Block Node needs to learn the current TSS state (roster, verification key, ledger ID)
    - Plugin implements the existing `BlockNodePlugin` interfaces
    - The plugin queries a peer BN's serverStatusDetail gRPC endpoint to retrieve TSS information
      - gRPC peer communication follows the same WebClient pattern used elsewhere in the codebase
    - The `ApplicationStateFacility` interface coordinates `BlockNodeContext` and `TssData` changes between plugins
      - Handles requests to change `TssData`
      - Uses the greatest `TssData.validFromBlock` to determine which `TssData` to use.
      - Persists the latest `TssData`
      - Notifies plugins when the `BlockNodeContext` changes on a separate thread.
      - Implemented by the `BlockNodeApp`
    - The plugin checks it's config at startup to see if `TSSData` is present in the config
    - `TssBootstrapPluginConfig` can be used for both testing and temporary initialization for a Block Node
    - The plugin will periodically query its peers for TSS data updates.
  - TSS data is exposed to other plugins
    - The BlockNode plugin interface contains an `onContextUpdate` method that is called by the `ApplicationStateFacility`, on a separate thread, when the context is updated.
    - The `StatusDetailPlugin` implements `onContextUpdate()` and receives TSS data updates from the `ApplicationStateFacility`

## Diagram

TBD

Consider using mermaid to generate one or more of the following:
- [User Journey](https://mermaid.js.org/syntax/userJourney.html)
- [Requirement Diagram](https://mermaid.js.org/syntax/requirementDiagram.html)
- [Sequence Diagram](https://mermaid.js.org/syntax/sequenceDiagram.html)
- [Class Diagram](https://mermaid.js.org/syntax/classDiagram.html)
- [Block Diagram](https://mermaid.js.org/syntax/block.html)
- [Architecture Diagram](https://mermaid.js.org/syntax/architecture.html)
- [Flowchart](https://mermaid.js.org/syntax/flowchart.html)
- [State Diagram](https://mermaid.js.org/syntax/stateDiagram.html)
- [Entity Relationship Diagram](https://mermaid.js.org/syntax/entityRelationshipDiagram.html)**

## Configuration

### ApplicationStateFacility (NodeConfig)

- `tssDataFilePath`. The file path to use to persist and load `TssData`
- `tssUpdatePollIntervalMillis`. The poll interval in milliseconds that the ApplicationStateFacility uses to check for
  `TssData` updates

### TssBootstrapPlugin (TssBootstrapConfig)

- `ledgerId`. Base64 encoded `ledgerId`
- `wrapsVerificationKey`. Base64 encoded `wrapsVerificationKey`
- `nodeId`. The node id
- `weight`. The weight
- `schnorrPublicKey`, Base64 encoded 'schnorrPublicKey'
- `validFromBlock`, The block number this 'TssData' is valid from
- `rosterValidFromBlock`, The block number this 'TssRoster' is valid from

## Metrics

TBD

## Exceptions

TBD

## Acceptance Tests

- TssData is loaded from disk at startup.
- TssData can be overwritten with config data, if the validFromBlock is greater than other sources.
- Plugins can notify the application that TssData has changed.
- The application will persist the latest TssData to disk.
- The application will notify plugins from a separate thread when BlockNodeContext has changed.

## FAQ

1. Plugin ordering — how does bootstrap init before verification? ServiceLoader doesn't guarantee order.
   - The `ApplicationStateFacility` will load persisted `TssData` and add it to the `ConcurrentLinkedQueue<TssData>` prior to calling
     `init()` on the `BlockNodePlugin`.
   - `init()` will be called on all plugins.
   - Plugins may or may not submit `TssData` updates.
   - The `ApplcationStateFacility` will be started. It will process the `ConcurrentLinkedQueue<TssData>` and will notify
     the plugins of the newest `TssData` based on the `validFromBlock` field of `TssData`
   - Plugins will be updated during their lifetime using the `BlockNodePlugin.onContextUpdate()`.
   - `BlockNodePlugin.onContextUpdate()` will be called from a separate thread. Plugins should take care to update
     their internal state correctly.
2. Will the `TssBootstrapPlugin` persist the .bin file into the Verification PVC?
   - No plugin will write to storage managed by another plugin.
   - Plugins should not share configuration information.
   - The verification plugin should manage its own configuration
   - The `ApplicationStateFacility` will be responsible for persisting the `TssData`
   - When the verification plugins detects new `TssData`, ie Block 0, it will notify the `ApplicationStateFacility`.
   - The `ApplicationStateFacility` will update all plugins of BlockNodeContext changes via `BlockNodePlugin.onContextUpdate()`
3. Is this plugin intended to run only once at BN first run.
   - The plugin will periodically query its peer BN servers to see if they have any `TssData` updates.
4. Where and how will it get the BN source(s) for trusted peers to get the TSS data from? We already have the concept of backfill peers.
   - Plugins should not share configuration information.
   - The `TssBootstrapPlugin` will manage its own configuration.
   - Perhaps the `ApplicationStateFacility` should manage the BN Peer information
     - BN peers for backfill are very likely not the same as BN peers for TSSData.
       In general the two should not try to coordinate.
