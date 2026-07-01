# Special-Purpose WRB Block Node — Design

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
11. [Open Questions](#open-questions)

## Purpose

Distribute historical and live **Wrapped Record Blocks (WRBs)** and **TSS** data from a WRB CLI
conversion server to Council-operated **Tier-1 Block Nodes**. A co-located **special-purpose Block
Node** acts as the initial source ("Tier 0"): the CLI pushes blocks into it through the normal
ingestion path, and Tier-1 nodes pull blocks (backfill) and query TSS (status API) from it.
To serve *historical* WRBs, the Block Node must verify each block against the address book that
was in effect for that block, not just the current one.

This is intended as the durable long-term solution (historical address-book verification), not
throwaway migration code.

## Goals

- Push historical WRBs from the CLI's existing wrapped-block storage into the special-purpose BN,
  via simple bulk load with the BN not running. The BN will detect the bulk data loaded on startup,
  and will accept blocks from that point forward on the Publish API.
- Keep the special-purpose BN current during the transition by pushing each new WRB live as
  the CLI produces it (no 6-hour zip-batch floods).
- Verify any historical WRB by selecting the address book in effect for that block's number.
- Let Tier-1 nodes use the special-purpose BN as a backfill source for WRBs and TSS details.
- Reuse existing code where possible to reduce overhead and throwaway.

## Terms

<dl>
  <dt>WRB (Wrapped Record Block)</dt>
  <dd>A legacy Hedera record file wrapped into the Block Stream block format, carrying a
      <code>SignedRecordFileProof</code> verified against node RSA keys.</dd>
  <dt>TSS data</dt>
  <dd>Threshold-signature-scheme material (ledger id, roster, WRAPS verification key) the BN needs;
      produced by the CLI as <code>tss-bootstrap-roster.json</code>.</dd>
  <dt>Special-purpose BN ("Tier 0")</dt>
  <dd>A Block Node co-located with the CLI that serves as the initial WRB source and TSS peer
      for Tier-1 nodes, effectively replacing the consensus node for WRB distribution.</dd>
  <dt>Address Book History</dt>
  <dd>An ordered set of historical address books, each scoped to a block-number range, used to verify
      a WRB against the keys that were valid when its underlying record file was signed.</dd>
  <dt>CLI</dt>
  <dd>The picocli-derived tool <code>org.hiero.block.tools.BlockStreamTool</code> under
      <code>tools-and-tests/tools</code> (commands <code>blocks</code>, <code>days</code>,
      <code>mirror</code>, <code>networkCapacity</code>, …).</dd>
</dl>

## Entities

- **CLI (`tools-and-tests/tools`)** — already wraps record files into WRBs (`blocks wrap`,
  `days live-sequential`), maintains address-book history (`mirror generateAddressBook*`,
  `compareAddressBooks`), and can push blocks over gRPC (`networkCapacity` client). Extended here with
  backfill-push and live-push.
- **Special-purpose Block Node** — standard BN deployment that ingests pushed blocks, stores
  them, verifies WRBs against the historical address-book history, and serves Tier-1 nodes.
- **Tier-1 Block Nodes** — Council-run; pull blocks via their existing backfill plugin and query TSS
  via `RosterBootstrapTssPlugin.queryPeerTssData()`.
- **Mirror Node** — source of address-book changes over time, used to build/maintain the address-book history.
- **Address-book history store** — the BN-side representation of historical address books keyed by
  block range (bootstrapped from CLI history, maintained from Mirror Node).

## Design

### 1. Push-based ingestion (CLI → special BN)

Historical blocks are loaded via bulk copy into the BN's storage layout while the BN is stopped; on
restart the BN detects the loaded data and accepts new blocks from that point via the Publish API.
Live blocks continue to be published one at a time through the normal ingestion endpoint.

- **Backfill (one-time):** a CLI script bulk-copies the existing wrapped-block zips (10k blocks/zip,
  produced ~every 6 hours) into the BN's storage layout while the BN is stopped. On startup the BN
  detects the loaded blocks and accepts new blocks from that point forward on the Publish API.
  Resumable: re-run copies only the remaining blocks.
- **Live (continuous):** the CLI's live wrap pipeline (`days live-sequential` / `download-live2`) is
  extended to publish each WRB immediately as it is produced, bypassing the in-memory 6-hour batching
  for the distribution path. Reuses the same publish client.

Bulk-load for the historical backfill (vs. one-block-at-a-time via the Publish API) is faster and
avoids publisher-side back-pressure; one-block-at-a-time for the live path keeps the BN continuously
current without flooding it.

### 2. Historical address-book history (BN)

Today the BN verifies WRBs against a single, current address book (`RsaRosterBootstrapPlugin` loads one
`NodeAddressBook`). To verify *historical* WRBs the BN holds a **history of address books, each scoped
to a `[startBlock, endBlock]` range**:

- **Model + store:** an ordered, range-keyed collection with O(log n) lookup by block number; extends
  `RsaRosterBootstrapPlugin` / `RsaRosterBootstrapConfig`.
- **Bootstrap:** load the CLI's historical address-book history (the ~20 JSON files the CLI already
  maintains) converted into the address-book history format. The conversion is an operator script/CLI step (see
  §Configuration and the tickets).
- **Maintenance:** the BN queries Mirror Node for address-book changes from genesis forward, translates
  the change's consensus time to a block number, and appends a new address-book entry — extending the RSA
  plugin's existing Mirror-Node query path.

#### 2a. On-disk roster file format

The BN's block-number-keyed address-book history is serialised as a **JSON-encoded
`RangedAddressBookHistory`** protobuf message, defined in
`protobuf-sources/src/main/proto/internal/ranged_address_book_history.proto`.

**Proto definition (summary):**

```proto
message RangedAddressBookHistory {
    repeated RangedNodeAddressBook address_books = 1;
}

message RangedNodeAddressBook {
    proto.NodeAddressBook address_book = 1;  // nodeId + RSA_PubKey per NodeAddress
    uint64 start_block = 2;                  // first block covered (inclusive)
    uint64 end_block   = 3;                  // last block covered (inclusive); 0 = open-ended
}
```

**Invariants operators and T3 tooling must maintain:**

|       Invariant       |                                        Description                                        |
|-----------------------|-------------------------------------------------------------------------------------------|
| Ordered ascending     | Entries must be sorted by `start_block` (lowest first).                                   |
| Non-overlapping       | No two entries may cover the same block number.                                           |
| Open-ended last entry | The final entry should use `end_block = 0` to cover all future blocks ≥ `start_block`.    |
| Non-empty             | The file must contain at least one entry; an empty list causes the BN to fail at startup. |

**Codec:** `RangedAddressBookHistory.JSON` (PBJ-generated). File is written/read using the same
atomic `.tmp`-then-rename pattern as the single-book `rsa-bootstrap-roster.json`.

**Default file path:** `/opt/hiero/block-node/application-state/rsa-address-book-history.json`
(configured via `app.state.rsaAddressBookHistoryFilePath`).

**Precedence at startup:** when the history file is present it takes precedence over the
single-book `rsa-bootstrap-roster.json`. When only the single-book file exists the BN wraps it
into a single open-ended era (`startBlock = 0`, `endBlock = 0`) so verification behaviour is
unchanged — a backward-compatibility bridge that requires no operator action for existing
single-book deployments.

**Minimal example (2-era mainnet history):**

```json
{
  "addressBooks": [
    {
      "addressBook": {
        "nodeAddress": [
          { "nodeId": "1", "RSA_PubKey": "3082..." },
          { "nodeId": "2", "RSA_PubKey": "3082..." }
        ]
      },
      "startBlock": "0",
      "endBlock":   "5000000"
    },
    {
      "addressBook": {
        "nodeAddress": [
          { "nodeId": "1", "RSA_PubKey": "3082..." },
          { "nodeId": "3", "RSA_PubKey": "3082..." }
        ]
      },
      "startBlock": "5000001",
      "endBlock":   "0"
    }
  ]
}
```

**Lookup algorithm (O(log n)):** The BN builds a `NavigableMap<startBlock, RangedNodeAddressBook>`
at startup via `AddressBookHistoryLookup.buildIndex()`. For a given block number `b`:

1. `floorEntry(b)` — find the entry with the largest `startBlock ≤ b`.
2. If no entry exists, or if `endBlock > 0 && b > endBlock`, the block falls outside all known
   eras → verification fails with a documented cause.
3. Otherwise, use the entry's `NodeAddressBook` for RSA key lookup.

### 3. Historical WRB verification by block number

WRB verification (`ExtendedMerkleTreeSession`, today a single `rsaKeyByNodeId` map) selects the address
book whose range contains the block being verified, and validates the `SignedRecordFileProof` against
that era's node RSA keys. A block with no available address book fails verification with a clear cause.

### 4. TSS distribution (no new code)

Tier-1 retrieval already exists via `RosterBootstrapTssPlugin.queryPeerTssData()` against the
special-purpose BN's status API. The only operational step is moving the CLI-produced
`/mnt/wrb-operations/wrappedBlocks/tss-bootstrap-roster.json` to the BN's
`/opt/hiero/block-node/application-state/` — a documented operator/script action, **not** an engineering change.
Stop the Block Node before copying the file into place and restart after — this avoids concurrent writes to the same location.

### Reuse summary

|                    Need                     |                                                   Reused code                                                   |
|---------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| Push blocks over gRPC                       | `tools/.../capacity/NetworkCapacityClient`, simulator `PublishStreamGrpcClient`; server side `stream-publisher` |
| Wrapped-block source + address-book history | CLI `blocks`/`days`/`mirror` commands                                                                           |
| Current RSA roster + WRB verification       | `RsaRosterBootstrapPlugin`, `block-verification`/`verification` `ExtendedMerkleTreeSession`                     |
| TSS peer retrieval                          | `RosterBootstrapTssPlugin.queryPeerTssData()`                                                                   |

## Diagram

```mermaid
flowchart LR
    subgraph WRBS [WRB server]
        CLI[CLI tools<br/>wrap + push] -->|"bulk-load (historical) / publish (live)"| SP[Special-purpose BN<br/>Tier 0]
        TSSJSON[tss-bootstrap-roster.json] -. operator copy .-> SPCFG[(BN config<br/>/opt/hiero/block-node/application-state)]
    end
    MN[Mirror Node] -->|address-book changes| SP
    ABH[CLI address-book history JSON] -. convert + bootstrap .-> SP
    SP -->|backfill blocks| T1[Tier-1 BNs]
    SP -->|queryPeerTssData status API| T1
    SP -->|verify WRB by block-range address book| SP
```

## Configuration

New / affected configuration (final keys decided per ticket; plugins own their own config):

- **Address-book history** (BN, `ApplicationStateConfig`):
  `app.state.rsaAddressBookHistoryFilePath` — path to the JSON-encoded `RangedAddressBookHistory`
  file (default: `/opt/hiero/block-node/application-state/rsa-address-book-history.json`).
  When present at startup this file takes precedence over the single-book
  `app.state.rsaBootstrapFilePath`. See §2a for the full file format.
- **Mirror-Node address-book maintenance** (BN): enable flag + MN endpoint (reuse the RSA plugin's existing
  MN settings).
- **CLI backfill-push**: source wrapped-block directory, target BN publish endpoint, start/resume block.
- **CLI live-push**: target BN publish endpoint (and enable/disable of the live distribution path).
- **TSS (operator step, no config change):** copy
  `/mnt/wrb-operations/wrappedBlocks/tss-bootstrap-roster.json` →
  `/opt/hiero/block-node/application-state/`.

## Metrics

- CLI backfill: blocks pushed, push rate, failures/retries, last-pushed block.
- BN ingestion: blocks accepted from the push source (existing publisher metrics).
- Verification: WRBs verified per address-book era, verification failures with "no address book"
  reason count.
- Address-book history: `blocknode:roster_eras_loaded` (number of `[startBlock, endBlock]` eras
  loaded), `blocknode:roster_entries_loaded` (total `NodeAddress` entries across all eras),
  `blocknode:roster_load_duration_ms` (startup load time in ms).

## Exceptions

- **Push failure / BN unavailable:** CLI retries with backoff; backfill is resumable from the BN's
  latest block; live push buffers/retries without dropping blocks.
- **No matching address book for a block:** verification fails fast with a clear cause; surfaced as a
  metric and log, not a silent pass.
- **Mirror-Node unavailable during maintenance:** address-book history keeps its last-known entries; retry later;
  bootstrap remains valid.
- **Duplicate/out-of-order pushes:** rely on the BN's normal ingestion validation (idempotent on
  already-stored blocks).

## Acceptance Tests

- Backfill bulk-loads the full historical WRB set into the special-purpose BN while it is stopped, and is
  resumable (re-run copies only the remaining blocks).
- Live push keeps the special-purpose BN current as the CLI produces new WRBs (no 6-hour gaps).
- A historical WRB from an earlier address-book era verifies successfully against the correct era's
  address-book entry; a block outside any address-book range fails with the documented cause.
- A Tier-1 BN backfills blocks from the special-purpose BN and verifies them.
- After the operator moves `tss-bootstrap-roster.json` into the BN config dir, a Tier-1 BN's
  `queryPeerTssData()` retrieves TSS data successfully.

## Open Questions

- **Address-book file delivery:** ship the address-book history as a code resource vs. require operator loading
  (leaning toward a documented script/tool that converts the CLI's address-book history).
- **Backfill-source API plugin:** whether the special-purpose BN should run a simplified API plugin
  set to minimize failure points (discussed, undecided).
- **Address-book change → block-number mapping:** block number is required (consensus-time lookup is too expensive for the BN); authoritative source for the mapping TBD (CLI block-times data vs. Mirror Node API).
- **Council positioning:** how to present the "Tier 0" classification without confusion (non-engineering).
- **TSS JSON ↔ BN schema:** confirm the CLI's `tss-bootstrap-roster.json` matches the BN's expected
  format (verification task; no code expected, but a gap here would add one).
