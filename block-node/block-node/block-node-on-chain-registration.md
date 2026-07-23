# Block Node On-Chain Registration (HIP-1137)

This document explains how Block Node operators publish their nodes to the on-chain registry defined in [HIP-1137](https://hips.hedera.com/hip/hip-1137), why it matters, and what it means for the operational lifecycle of a Block Node.

## Overview

Before this on-chain registry, finding a Block Node meant relying on a published list, a deployment ticket, or a direct relationship with the operator - there was no on-chain way to discover Block Nodes. The registry changes that: operators publish their node type and service endpoints via a HAPI transaction, and Mirror Nodes, RPC Relays, and SDKs query the network to find them.

Registration is supplementary to existing operational practice - your Block Node continues to function without being registered - but it is what makes your node discoverable:

- **Clients find your Block Node by querying the network rather than by trusting a hard-coded list:** Mirror Nodes, Relays, and SDKs rely on an authoritative on-chain source.
- **Your operator identity is on-chain:** the administrative key that controls your registered node is a verifiable identity clients can use to know who runs the service.
- **Updates are self-sovereign:** when you change endpoints, rotate keys, or move a service behind TLS, you publish the change yourself via a signed transaction - no central list has to be updated for you.

For a [**Tier 1**](./glossary.md#tier-1-block-node) Block Node, registration is how Mirror Node operators who consume your stream learn that your node exists. For a [**Tier 2**](./glossary.md#tier-2-block-node) Block Node operated as a local buffer for many Mirror Nodes (see [Mirror Node Integration](./mirror-node-integration.md)), registration is optional but recommended if you want others to subscribe to your buffer.

## Availability across networks

The on-chain registry lands in the following release tags:

|   Component    | Release  |                                                                                                                                                                                               What it brings                                                                                                                                                                                               |
|----------------|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Consensus Node | `v0.75`  | The `RegisteredNodeCreate` / `RegisteredNodeUpdate` / `RegisteredNodeDelete` HAPI transactions (HieroFunctionality codes 101 / 102 / 103). Handlers live at [`hedera-node/hedera-addressbook-service-impl/.../handlers/`](https://github.com/hiero-ledger/hiero-consensus-node/tree/main/hedera-node/hedera-addressbook-service-impl/src/main/java/com/hedera/node/app/service/addressbook/impl/handlers). |
| Mirror Node    | `v0.156` | The `/api/v1/network/registered-nodes` REST endpoint and the `associated_registered_nodes` field on `/api/v1/network/nodes`. Implemented at [`rest-java/.../NetworkController.java`](https://github.com/hiero-ledger/hiero-mirror-node/blob/main/rest-java/src/main/java/org/hiero/mirror/restjava/controller/NetworkController.java).                                                                     |

The rollout dates for `v0.75` on each network are network-operations decisions - check the [Consensus Node](https://github.com/hiero-ledger/hiero-consensus-node/releases) and [Mirror Node](https://github.com/hiero-ledger/hiero-mirror-node/releases) release pages before scheduling your first registration on mainnet.

## What gets registered

The `RegisteredNode` record stored in network state has the following fields:

|           Field           |         Required          |                                                                                                                                  Meaning                                                                                                                                  |
|---------------------------|---------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `registered_node_id`      | yes (assigned by network) | A `uint64` assigned by the network on creation. Unique within a shard/realm and never reused, even after deletion. Distinct from any consensus-node ID in the same network.                                                                                               |
| `admin_key`               | yes                       | The key that controls this entry. Must sign every create / update / delete transaction targeting this node. Recommended to be one or more public keys **not associated with any network account**. May be a `KeyList` or `ThresholdKey`; should not be a contract ID key. |
| `description`             | optional                  | Free-form text, ≤ 100 bytes UTF-8. Useful for distinguishing nodes (`alpha`, `us-east-archive-1`, etc.).                                                                                                                                                                  |
| `service_endpoint` (list) | yes                       | At least 1, at most 50 entries. Each describes one service endpoint. A single registered node may expose endpoints serving multiple node types or multiple different `BlockNodeApi` values.                                                                               |
| `node_account`            | optional                  | An `AccountID` that identifies the entity financially responsible for the node. May be different from the operator. May be omitted, set, changed, or removed at the operator's discretion.                                                                                |

The `registered_node_id` is returned in the `TransactionReceipt` of a successful `createRegisteredNode` - record it; you will need it for every subsequent update or delete.

## Service endpoints

Each entry in `service_endpoint` carries (per the `RegisteredServiceEndpoint` message):

- An address - either an `ip_address` (IPv4 or IPv6, big-endian byte order) or a `domain_name` (FQDN, ≤ 250 ASCII characters). The two are mutually exclusive per endpoint.
- A `port` (`uint32`, `0`–`65535`).
- A `requires_tls` flag. If `true`, clients must connect over TLS. Self-signed certificates are permitted for testing but should not be used in production.
- A `block_node` discriminator carrying a list of `BlockNodeApi` values declaring which API(s) the endpoint serves. The `block_node` discriminator is one of several endpoint discriminators the registry supports (others cover mirror node, relay node, and additional node types); it is included only when the endpoint represents a Block Node.

The `BlockNodeApi` values:

|       Value        |                                                                    Meaning                                                                    |
|--------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| `STATUS`           | The Block Node Status API (`BlockNodeService.serverStatus` and related).                                                                      |
| `PUBLISH`          | The Block Node Publish API (Consensus Nodes publish block streams here).                                                                      |
| `SUBSCRIBE_STREAM` | The Block Node Subscribe API (Mirror Nodes and other consumers read block streams here).                                                      |
| `STATE_PROOF`      | The Block Node State Proof API.                                                                                                               |
| `OTHER`            | Any other Block Node API; the consumer must consult node-specific documentation and is recommended to query the detail status endpoint first. |

A single endpoint may declare multiple values from this enum if it serves more than one API on the same host/port. Most production deployments split APIs across endpoints - for example, one endpoint for `PUBLISH` and a separate endpoint for `SUBSCRIBE_STREAM` - because the bandwidth and security profiles differ. Even when endpoints are spread across different hosts, they all belong to one logical node — any client connecting to any of them must see the same block data. The default gRPC port for the Block Node is `40840`; see [Block Node Configuration](./configuration.md) for the operator-side configuration of the underlying service.

### Which APIs to declare for your deployment

Not every Block Node serves every API. Use the following as a starting point and trim it to what your deployment actually exposes:

|                            Deployment shape                            |                            Recommended `endpoint_api` set                             |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| Tier 1 receiving from Consensus Nodes and feeding downstream consumers | `PUBLISH`, `SUBSCRIBE_STREAM`, `STATUS`                                               |
| Tier 2 buffer in front of Mirror Nodes (no upstream from Consensus)    | `SUBSCRIBE_STREAM`, `STATUS` (add `STATE_PROOF` if the node serves proofs to clients) |
| Archive node serving historical retrieval, not live ingest             | `SUBSCRIBE_STREAM`, `STATUS` (no `PUBLISH`)                                           |
| Specialized or experimental APIs not in the enum                       | `OTHER` (consumers must consult node-specific documentation)                          |

Declare only the APIs each endpoint truly serves. A declared but unimplemented API surfaces as a bug to clients that route to it on the basis of the registry.

### Which address to declare

`service_endpoint` is the address a client will use to reach your Block Node. In a typical Kubernetes deployment with several internal and external addresses, register only the **externally reachable** address - the LoadBalancer or ingress hostname / IP that clients can actually dial.

The Block Node process itself does not terminate TLS (see [Block Node Configuration](./configuration.md)). If TLS termination happens at an upstream load balancer or ingress, set `requires_tls = true` and confirm the certificate the load balancer presents is one the clients can validate against. Do **not** register internal-only cluster addresses such as `ClusterIP` services or pod IPs; those are not reachable from outside the cluster.

## Registration lifecycle

Three operator-visible steps. All three transactions also support deferred execution via `SchedulableTransactionBody` if a registry change needs to be coordinated with other approvals or scheduled for a specific time.

### Step 1: Create the registration

Submit a `RegisteredNodeCreateTransactionBody`, signed by the new `admin_key`. On success, the transaction receipt carries the assigned `registered_node_id` - **record this value safely**; it is your handle for every subsequent update or delete. If lost, recover it by listing all Block Node registrations on the network and filtering by your `admin_key`, endpoint host, or description:

> **Mainnet only:** On Hedera mainnet, `RegisteredNodeCreate` is a privileged transaction. It can only be submitted by a payer account in the range `0.0.2`–`0.0.55`.

```bash
curl -s "https://{MIRROR_NODE_HOST}/api/v1/network/registered-nodes?type=BLOCK_NODE" \
  | jq '.registered_nodes[] | select(.service_endpoints[]?.domain_name == "{YOUR_ENDPOINT_HOST}")'
```

#### Worked example

A Tier 1 mainnet Block Node operated at `bn.example.com` would typically register with two endpoints:

```text
RegisteredNodeCreateTransactionBody
  admin_key:    ThresholdKey(2-of-3, three operator-controlled Ed25519 keys, not tied to a network account)
  description:  "acme-mainnet-1"
  node_account: 0.0.1234567   # operator's billing account
  service_endpoint:
    - domain_name:  "bn.example.com"
      port:         40840
      requires_tls: true
      block_node:
        endpoint_api: [PUBLISH, SUBSCRIBE_STREAM, STATUS]
    - domain_name:  "bn-proof.example.com"
      port:         40840
      requires_tls: true
      block_node:
        endpoint_api: [STATE_PROOF]
```

The two endpoints let the live ingest/stream and state-proof paths scale and be load-balanced independently. Single-endpoint registrations are also valid.

#### Submit the transaction

Two paths:

- **`yahcli`** - the DevOps CLI bundled with consensus-node. The `registerednodes create / update / delete` subcommands wrap the three transactions directly. Source at [`hedera-node/yahcli/.../commands/registerednodes/`](https://github.com/hiero-ledger/hiero-consensus-node/tree/main/hedera-node/yahcli/src/main/java/com/hedera/services/yahcli/commands/registerednodes); usage in [`hedera-node/yahcli/README.md`](https://github.com/hiero-ledger/hiero-consensus-node/blob/main/hedera-node/yahcli/README.md).
- **Any official Hiero SDK** - all seven expose `RegisteredNodeCreateTransaction` (and Update / Delete equivalents): [Java](https://github.com/hiero-ledger/hiero-sdk-java), [JavaScript](https://github.com/hiero-ledger/hiero-sdk-js), [Go](https://github.com/hiero-ledger/hiero-sdk-go), [Rust](https://github.com/hiero-ledger/hiero-sdk-rust), [Swift](https://github.com/hiero-ledger/hiero-sdk-swift), [C++](https://github.com/hiero-ledger/hiero-sdk-cpp), [Python](https://github.com/hiero-ledger/hiero-sdk-python). All SDKs also expose `PrivateKey.generateED25519()` (or the language-equivalent) for `admin_key` generation; an external keygen tool is only needed if your operational policy requires one (HSM, KMS, etc.). The [Hiero SDKs index](https://docs.hiero.org/sdks) is the entry point for SDK-specific guides.

The flow is the same regardless of path: build the transaction body using the fields from the [Worked example](#worked-example), sign with the `admin_key`, execute against the target network's gRPC endpoint, and read the `TransactionReceipt` to capture the assigned `registered_node_id`.

#### Verify the registration

After the create transaction returns `SUCCESS`, confirm the entry is visible through the Mirror Node REST surface:

```bash
curl -s "https://{MIRROR_NODE_HOST}/api/v1/network/registered-nodes?registerednode.id=eq:{YOUR_REGISTERED_NODE_ID}"
```

Expected response shape (trimmed):

```json
{
  "registered_nodes": [
    {
      "admin_key": { "_type": "ProtobufEncoded", "key": "<your-public-key-hex>" },
      "created_timestamp": "1586567700.453054001",
      "description": "acme-mainnet-1",
      "registered_node_id": 12345,
      "service_endpoints": [
        {
          "block_node": { "endpoint_apis": ["PUBLISH", "SUBSCRIBE_STREAM", "STATUS"] },
          "domain_name": "bn.example.com",
          "ip_address": null,
          "port": 40840,
          "requires_tls": true,
          "type": "BLOCK_NODE"
        }
      ],
      "timestamp": { "from": "1586567700.453054001", "to": null }
    }
  ]
}
```

Confirm that `registered_node_id`, `service_endpoints` (host, port, `endpoint_apis`, `requires_tls`), `admin_key`, and `description` match what you submitted. Mirror Nodes index the registry from block-stream data, so allow a short propagation delay (typically seconds) before treating a missing entry as a failure.

### Step 2: Update the registration

Submit a `RegisteredNodeUpdateTransactionBody` whenever a registered field changes:

- Endpoints change - you move to a new host, add a new API, add or remove TLS, etc.
- You rotate the `admin_key`.
- You change the `node_account` (set to `0.0.0` to remove it).
- The `description` should change.

The update transaction must be signed by the **current** `admin_key`. If you set a new `admin_key` in the same update, the new key applies from the next transaction onward. Updates are **replace** semantics on the `service_endpoint` list - if you set it, the whole list is replaced, not patched.

#### Rotating the `admin_key`

1. Generate the new key (single key, `KeyList`, or `ThresholdKey`) and store it in the operator's secret-management system **before** submitting any transaction. Losing the new key after step 3 below leaves the registration unreachable.
2. Compose a single `RegisteredNodeUpdateTransactionBody` that sets `admin_key` to the new key and changes no other fields you do not need to.
3. Sign with **both** the **current** `admin_key` and the **new** `admin_key`. Submit.
4. On `SUCCESS`, the new key is authoritative. Every subsequent update or delete must be signed with the new key.
5. Verify by submitting a no-op-style update (for example, re-publishing the existing `description`) signed with the new key, and confirming `SUCCESS`. Only after that confirmation should the old key be destroyed.

If your registration uses a `KeyList` or `ThresholdKey`, you can rotate one member at a time without ever being keyless - the recommended posture for production.

#### After a reset or upgrade

A Block Node software upgrade or [data reset](./operations/resetting-and-upgrading-the-block-node.md) does **not** affect the registration. Submit a `RegisteredNodeUpdateTransactionBody` only if the upgrade or reset changes a registered field (endpoint, key, account, description).

### Step 3: Delete the registration

Submit a `RegisteredNodeDeleteTransactionBody` with the `registered_node_id`. Must be signed by the current `admin_key`, or authorized by the network's governance structure (which an operator should rely on only in administrative-takeover scenarios).

If your `registered_node_id` is currently listed in any consensus node's `associated_registered_nodes` list, the delete will be rejected until you remove it from that list first — see [Linking to a consensus node](#linking-to-a-consensus-node-optional) below for the `NodeUpdate` procedure.

A deleted `registered_node_id` is gone for good - the value will not be reused for any future registration, so even if you re-register the same Block Node you will receive a new `registered_node_id`.

### Linking to a consensus node *(optional)*

If you also operate a Hiero consensus node, you can declare the association between the two via the `Node.associated_registered_node` list on the consensus-node side (up to 20 entries per consensus node). This is managed by the existing `NodeUpdate` transaction on the consensus-node side; the registered-node side requires no separate action.

To remove an association before deleting a registered node, submit `NodeUpdate` with the updated `associated_registered_node_list` (omit the `registered_node_id` you are about to delete, or set the list to empty to clear all associations).

## Fees and throttles

All three transactions share a fee schedule and throttle configuration identical across previewnet, testnet, and mainnet at `v0.75`. The base fee is `9_000_000` tinycents (≈ $0.09 USD at the schedule's pegged rate) per the [`simpleFeesSchedules.json`](https://github.com/hiero-ledger/hiero-consensus-node/blob/main/hedera-node/configuration/mainnet/upgrade/simpleFeesSchedules.json) format; the full schedule lives in [`feeSchedules.json`](https://github.com/hiero-ledger/hiero-consensus-node/blob/main/hedera-node/configuration/mainnet/upgrade/feeSchedules.json).

`RegisteredNodeCreate` is rate-limited network-wide at ≈ 2 ops/sec (`milliOpsPerSec = 2000`), sharing a throttle bucket with `CryptoCreate` and `NodeCreate` per [`throttles.json`](https://github.com/hiero-ledger/hiero-consensus-node/blob/main/hedera-node/configuration/mainnet/upgrade/throttles.json). `RegisteredNodeUpdate` and `RegisteredNodeDelete` fall under the standard `ThroughputLimits` bucket and are not separately rate-limited. For a planned onboarding of multiple Block Nodes, sequence the create transactions with **at least 1 second between submissions** and be prepared to retry on throttle rejection — the bucket is shared with other consensus-network-level create operations, so your slot is not guaranteed.

## Recommended practices

- **Test on previewnet or testnet before mainnet.** HIP-1137 ships on all three networks together at `v0.75`; submit a throwaway registration on a non-production network first to validate your transaction shape, key custody, and verification steps. There is no recovery from a lost `admin_key`, and the create throttle is network-wide — both reasons to dry-run.
- **Use an `admin_key` not tied to any network account.** The administrative key controls the registration; entangling it with a treasury or transaction-signing account adds unnecessary blast radius if the operator account is ever compromised.
- **Use a `KeyList` or `ThresholdKey` for production.** Single-key control is acceptable for dev/test but exposes the registration to single-point-of-failure key loss.
- **Treat the `registered_node_id` as operational metadata.** Store it alongside your deployment records. Without it, every subsequent management transaction requires looking your node up from the registry first.
- **Plan endpoint changes as transactions, not edits.** Because updates replace the entire `service_endpoint` list, the operator must submit the complete desired endpoint set, not a delta. Treat endpoint changes the same way as infrastructure changes - in a change-management ticket, reviewed before submission.
- **Keep `node_account` current.** If the account is set but resolves to a deleted or invalid account, the next update transaction will fail. Fix it by submitting a `RegisteredNodeUpdateTransactionBody` that sets `node_account` to a valid `AccountID`, or to `0.0.0` to clear it.

## How clients find your Block Node

Three surfaces are exposed by the existing Hiero infrastructure once you are registered:

- **Mirror Node REST API.** `GET /api/v1/network/registered-nodes` returns the full registry with the protobuf-encoded `admin_key`, the service endpoints, the timestamps, and the `registered_node_id` for each entry. Clients filter with `type=BLOCK_NODE`, paginate via `limit` and `registerednode.id`, and sort via `order`. See the [Mirror node update](https://hips.hedera.com/hip/hip-1137#mirror-node-update) section of the HIP for the exact response shape.
- **Automatic Mirror Node pickup.** Mirror Nodes running with `hiero.mirror.importer.block.autoDiscoveryEnabled = true` pick up your registration from the registry without any per-Mirror-Node configuration change - see [hiero-mirror-node#13013](https://github.com/hiero-ledger/hiero-mirror-node/issues/13013) and the [Mirror Node Integration](./mirror-node-integration.md) guide for the consumer side.
- **Consensus node address book.** The existing `/api/v1/network/nodes` endpoint now includes an `associated_registered_nodes` field on each consensus-node entry, listing the registered nodes operated by the same entity.

## Backwards compatibility

The on-chain registry is entirely net-new functionality. Existing transactions, message types, and APIs are unaffected. A Block Node that does not register continues to function exactly as before - clients that already know its address will still connect. Only discoverability via the registry is gated on registration.

## Security considerations

**`admin_key` exclusivity - no recovery if lost.** Every create, update, and delete on a registered node must be signed by that node's `admin_key`. **If the `admin_key` is lost, there is no recovery mechanism** - the network cannot re-issue the key, and the registration cannot be reassigned. The most that the network's governance structure (on mainnet, the Hedera Council) can do is sign a `Delete` transaction to remove the orphaned registration, after which the operator must create a new one with a fresh `registered_node_id`. To avoid the situation: use a `ThresholdKey` so any single key loss is recoverable from the surviving members, and consider Decentralized Recovery or HSM-backed custody for production-critical key material.
