# Special-Purpose (Tier-0) WRB Block Node — Operator Runbook

## Overview

A **special-purpose Block Node** (the "Tier-0" or "SP-BN") is a Block Node co-located with the
[WRB CLI](wrb-cli-runbook.md) whose sole job is to ingest **Wrapped Record Blocks (WRBs)** produced
by the CLI and re-serve them, along with the **address-book history** they were signed under and
the **TSS data**, to Council-operated Tier-1 Block Nodes.

The CLI feeds the SP-BN through two ingest paths that cover different block ranges:

- **Historical backfill** — the bulk of the archive is loaded directly onto the SP-BN's `archive`
  PV without going through the gRPC Publish API. The CLI's `blocks bulk-load` command (or a `tar`
  stream over `kubectl exec`) copies wrapped-block zips into the on-pod historic directory, and
  `BlockFileHistoricPlugin` picks them up on the next pod start. This is how the majority of
  blocks arrive on a fresh SP-BN.
- **Live push** — once the archive is caught up, the CLI's `days live-sequential --push-enabled`
  streams newly-wrapped blocks over the normal Publish API. This carries the tail-of-chain going
  forward.

Tier-1 nodes pull blocks, address books, and TSS data through the Block Node's standard
backfill and status paths.

The address-book history is a first-class serving responsibility, not just a local verification
concern. Every Tier-1 that reads WRBs from an SP-BN must verify each historical block against the
address book that was in effect for that block, so it must first fetch the same range-keyed history
from the SP-BN before it can start reading blocks. The SP-BN both loads its own range-keyed history
at startup and serves that history to downstream Tier-1s — a standard Tier-1 needs only a single
current bootstrap roster.

This runbook covers the SP-BN-specific deployment and operational steps. For the CLI-side
work that feeds the SP-BN, see the [WRB CLI runbook](wrb-cli-runbook.md); for the generic
pre-cutover BN checks (health, hardware, storage sizing) see [Preparing Your Block Node for WRB
Cutover](preparing-your-block-node-for-wrb-cutover.md). The end-to-end design lives at
[sp-wrb-bn-design.md](../../design/wrb-streaming/sp-wrb-bn-design.md).

**Audience**: operators standing up or maintaining an SP-BN (Solo/GKE, self-hosted Kubernetes, or
local kind).

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Deployment](#deployment)
3. [Address-book history bootstrap](#address-book-history-bootstrap)
4. [TSS bootstrap file move](#tss-bootstrap-file-move)
5. [Historical backfill](#historical-backfill)
6. [Live push](#live-push)
7. [Serving Tier-1 nodes](#serving-tier-1-nodes)
8. [Monitoring](#monitoring)
9. [Troubleshooting](#troubleshooting)
10. [Reference](#reference)

---

## Prerequisites

- A running Kubernetes cluster reachable via `kubectl` (Solo/GKE, kind, on-prem, etc.).
- `kubectl` and `helm` on your PATH, current context pointed at the target cluster.
- The **CLI shaded jar** for a release **at or after `v0.40.1`** — earlier releases pre-date the
  ranged address-book history loader described below. Download `block-stream-tools-<tag>.jar` from
  the [Block Node release page](https://github.com/hiero-ledger/hiero-block-node/releases); no repo
  clone or Gradle build is required. Pick a tag that matches (or trails) the chart / image tag you
  plan to deploy — the CLI's wrap output format must be understood by the BN.
- Address-book history JSON produced by the CLI's `mirror generateAddressBook*` command (see
  [wrb-cli-runbook.md § Generate Address Book History](wrb-cli-runbook.md#1-generate-address-book-history)).
- A directory of wrapped-block zips produced by the CLI's `blocks wrap` /
  `days live-sequential` (see [wrb-cli-runbook.md § Wrap Record Files](wrb-cli-runbook.md#4-wrap-record-files)).
- The CLI-produced `tss-bootstrap-roster.json` on the operator host.

> **Version alignment matters.** The BN main container image, the chart's init-container images,
> and the chart-templated startup command all reference the same version. Bumping one without the
> others (e.g. `kubectl set image` while leaving the chart on the old version) leaves the pod
> looking for `/opt/hiero/block-node/app-<oldver>/bin/app` inside an image that ships
> `app-<newver>/bin/app`, and the container exits immediately. Always upgrade via
> `helm upgrade`, never via `kubectl set image` on a Helm-managed StatefulSet.

---

## Deployment

The recommended path is **Solo Provisioner** (`solo-provisioner`), which bootstraps the on-box
Kubernetes cluster, provisions the PVs, installs the chart, and reconciles configuration changes
against a persisted state file. Both the previewnet and testnet SP-BNs are deployed and maintained
this way. Raw Helm is documented in [§ Deploying without Solo Provisioner](#deploying-without-solo-provisioner)
as a fallback for local `kind` and clusters where Solo Provisioner is not available.

### Prerequisites for the Solo Provisioner path

- `solo-provisioner` installed on the target host (see the [Solo Weaver docs](https://docs.hiero.org/block-node-home/deployment/solo-weaver-single-node-k8s-deployment)).
- A **provisioner config** YAML (profile, chart reference, storage paths, sizes) — one per
  network. Example: `~/testnet-lfh-provisioner-config.yaml`.
- A **chart values overlay** YAML (LFH preset, LoadBalancer, plugin ports). Start from
  [`charts/block-node-server/values-overrides/lfh-values.yaml`](../../../charts/block-node-server/values-overrides/lfh-values.yaml)
  and adapt for your network.

The LoadBalancer block in the values overlay **must** set `includePluginPorts: true` so the
publish (40984), subscribe (40980), block-access (40981), status (40982), and health (40983)
plugin ports are all exposed alongside the aggregate port (40840). Without it the external LB only
publishes 40840 and clients pinning individual ports (e.g. LiveSequential's `--push-bn-port`) will
be unable to reach the BN.

### Install

```bash
sudo solo-provisioner block node install \
  -p <profile>                                 \  # e.g. previewnet | testnet | mainnet
  --config  ~/<network>-lfh-provisioner-config.yaml \
  --values  ~/<network>-lfh-values.yaml         \
  --skip-hardware-checks                       \  # only if the box is under the strict quota
  --non-interactive
```

The provisioner bootstraps the single-node cluster (cri-o, kubelet, kubeadm, cilium, MetalLB,
helm) on first invocation, then deploys the chart. Subsequent changes to either YAML are applied
via `reconfigure` rather than reinstall:

```bash
sudo solo-provisioner block node reconfigure \
  -p <profile>                                 \
  --config  ~/<network>-lfh-provisioner-config.yaml \
  --values  ~/<network>-lfh-values.yaml         \
  --skip-hardware-checks                       \
  --non-interactive
```

`reconfigure` re-runs the helm upgrade with the new values and rolls the pod. It is the correct
tool for tasks like adding `includePluginPorts: true` after an initial install, or bumping the
chart version pin.

Confirm the pod reaches `1/1 Ready` before continuing:

```bash
kubectl rollout status statefulset/block-node-block-node-server \
  -n block-node --timeout=300s
```

### Persistent volume layout

A default install provisions five PVCs (per-pod, via `volumeClaimTemplates`):

|              PVC              |                               Purpose                                |
|-------------------------------|----------------------------------------------------------------------|
| `application-state-storage-*` | RSA/roster bootstrap, TSS parameters, verification state.            |
| `archive-storage-*`           | Historical archives — the target of the bulk-load path (§5).         |
| `live-storage-*`              | Live-received blocks pending archival.                               |
| `plugins-storage-*`           | Version-specific plugin JARs resolved by the `resolve-plugins` init. |
| `logging-storage-*`           | Per-pod log storage.                                                 |

**Do not delete PVCs to reset state on static-PV setups.** Deleting the PVC releases the PV; on
`Retain` reclaim (the default for hand-provisioned PVs) the PV stays with the old data attached
and refuses to rebind to a freshly-recreated PVC. Wipe file contents with a short-lived pod that
mounts the existing PVC instead — see the [Wipe recipe](#wipe-recipe) in Troubleshooting.

### Deploying without Solo Provisioner

For local `kind` clusters or environments where Solo Provisioner is not available, the chart can
be installed directly with Helm. The operator is then responsible for the cluster, PVs, and
LoadBalancer that Solo Provisioner would otherwise manage.

```bash
# From the cloned hiero-block-node repo at the target tag
helm dependency update ./charts/block-node-server
helm upgrade --install block-node ./charts/block-node-server \
  --namespace block-node --create-namespace \
  --values <your-values.yaml>
```

Confirm the pod reaches `1/1 Ready`:

```bash
kubectl rollout status statefulset/block-node-block-node-server \
  -n block-node --timeout=300s
```

The same `includePluginPorts: true` requirement applies to the values overlay when a
LoadBalancer is used. All later sections of this runbook (address-book bootstrap, TSS file move,
backfill, live push) apply unchanged regardless of which deployment path was used.

---

## Address-book history bootstrap

The BN verifies WRBs against the address book that was current *at the block being verified* — not
just today's roster. Operators supply that history by converting the CLI's per-era address-book
JSON into the BN's block-range-keyed format and placing the result at the RSA bootstrap path.

### Convert with the CLI

```bash
java -jar tools-*-all.jar --network <network> blocks convert-address-book-history \
  -i wrappedBlocks/addressBookHistory.json \
  -o rsa-bootstrap-roster.json
```

Key options (`--help` for the full list):

|         Flag         |                                                Description                                                 |
|----------------------|------------------------------------------------------------------------------------------------------------|
| `-i` / `--input`     | The CLI-produced `addressBookHistory.json`.                                                                |
| `-o` / `--output`    | Roster history JSON output. Default: `/opt/hiero/block-node/application-state/rsa-bootstrap-roster.json`.  |
| `--block-times-file` | `block_times.bin` used to translate consensus times to block numbers. Default: `metadata/block_times.bin`. |

The output is a `RangedAddressBookHistory` — an ordered list of `[startBlock, endBlock]` eras, each
carrying a `NodeAddressBook`. It replaces the single-book `NodeAddressBook` JSON that a Tier-1 BN
would normally load.

### Invariants the output must satisfy

The BN's roster loader (`AddressBookHistoryLookup`) enforces:

- **Ordered ascending** by `startBlock`.
- **Non-overlapping** — no two eras cover the same block.
- **Open-ended last entry** — the final era uses `endBlock = 0` to cover all future blocks
  ≥ `startBlock`.
- **Non-empty** — an empty list causes the BN to fail at startup.

The convert tool produces a compliant file. If you hand-edit, re-run `convert-address-book-history`
or verify by loading the file into a temporary BN and watching startup logs.

### Place the file on the BN

```bash
# Local file → BN application-state PVC (BN can be running; it re-reads on restart)
kubectl cp rsa-bootstrap-roster.json \
  block-node/block-node-block-node-server-0:/opt/hiero/block-node/application-state/rsa-bootstrap-roster.json \
  -c block-node-server
kubectl rollout restart statefulset/block-node-block-node-server -n block-node
```

### Config key

The BN reads the roster from `app.state.rsaBootstrapFilePath` (default
`/opt/hiero/block-node/application-state/rsa-bootstrap-roster.json`). The **same key and same file
path** accept either a single-book `NodeAddressBook` JSON or a ranged `RangedAddressBookHistory`
JSON — the loader auto-detects. This means bootstrapping a fresh SP-BN and upgrading an existing
Tier-1 to historical-verification behavior use the same file swap; no config change is required.

### Mirror-Node-driven maintenance (T6)

Once bootstrapped, the BN keeps its address-book history current by polling Mirror Node for new
address-book changes and appending eras as they land. Enable it via the RSA-roster plugin config
(all keys are `roster.bootstrap.rsa.*`):

|                          Key                           |                             Purpose                             | Default  |
|--------------------------------------------------------|-----------------------------------------------------------------|----------|
| `roster.bootstrap.rsa.mirrorNodeBaseUrl`               | Mirror Node REST endpoint. Empty disables the maintenance loop. | `""`     |
| `roster.bootstrap.rsa.mnInitialQueryIntervalMillis`    | First-poll interval.                                            | `5_000`  |
| `roster.bootstrap.rsa.mnSubsequentQueryIntervalMillis` | Steady-state poll interval.                                     | `60_000` |
| `roster.bootstrap.rsa.mirrorNodePageSize`              | Page size for the changes query.                                | `100`    |

For the SP-BN role, point `mirrorNodeBaseUrl` at the Mirror Node covering the same network as your
CLI (`https://mainnet.mirrornode.hedera.com`, `https://testnet.mirrornode.hedera.com`, etc.).

---

## TSS bootstrap file move

The BN's TSS peer retrieval (used by Tier-1 nodes via `RosterBootstrapTssPlugin.queryPeerTssData()`)
requires a single operator step: place the CLI-produced `tss-bootstrap-roster.json` at the BN's
application-state path.

**Critical: stop the BN before the copy, restart after.** The BN can and does rewrite this file
during normal operation; a concurrent write from an operator races the BN and can leave a corrupt
file.

```bash
# 1. Scale down so no BN process is writing the file
kubectl scale statefulset/block-node-block-node-server -n block-node --replicas=0
kubectl wait pod -l app.kubernetes.io/instance=block-node -n block-node \
  --for=delete --timeout=120s

# 2. Copy the CLI-produced file into the application-state PVC via a short-lived pod
#    (see Wipe recipe in Troubleshooting for the general pattern)
#    OR use `kubectl cp` into the BN pod after scale-up if the file is being *added*
#    to a running BN whose file doesn't exist yet.

# 3. Scale back up
kubectl scale statefulset/block-node-block-node-server -n block-node --replicas=1
kubectl rollout status statefulset/block-node-block-node-server -n block-node --timeout=300s
```

Do **not** write directly to `/mnt/wrb-operations/wrappedBlocks/tss-bootstrap-roster.json` if that
path is bind-mounted into the BN — the BN treats the destination as owned state, and a shared
write path races.

For long-lived SP-BN deployments, consider folding the copy into the deploy script that installs or
upgrades the Helm chart so the file is placed atomically alongside the pod bring-up.

---

## Historical backfill

Historical WRBs are bulk-copied into the BN's on-disk historic archive **while the BN is stopped**
(or, in practice, while it is running — the BN re-scans on its next restart). A single script
handles staging the wrapped-block zips and streaming them into the pod:

```bash
./backfill-wrb-to-bn.sh <path-to-wrappedBlocks-dir>
```

The script (`backfill-wrb-to-bn.sh`) is env-var configurable for non-default deployments:

|        Env var        |                    Meaning                     |                  Default                   |
|-----------------------|------------------------------------------------|--------------------------------------------|
| `BN_KUBE_CONTEXT`     | `kubectl` context to use.                      | current context                            |
| `BN_NAMESPACE`        | Namespace of the BN release.                   | `block-node`                               |
| `BN_STATEFULSET`      | StatefulSet name.                              | `block-node-block-node-server`             |
| `BN_POD`              | Pod name.                                      | `${BN_STATEFULSET}-0`                      |
| `BN_CONTAINER`        | Container name in the pod.                     | `block-node-server`                        |
| `HISTORIC_MOUNT_PATH` | On-pod historic dir.                           | `/opt/hiero/block-node/data/historic`      |
| `STAGING_DIR`         | Local staging dir.                             | `/tmp/bn-backfill-<pid>`                   |
| `CLI_JAR`             | Path to the CLI shadow jar.                    | hunts for `tools-*-all.jar` next to script |
| `READY_TIMEOUT`       | Seconds to wait for pod-ready after restart.   | `300`                                      |
| `SKIP_ROLLOUT`        | `true` to skip the auto-rollout after staging. | `false`                                    |

What it does, in order:

1. Waits for the BN pod to be `Ready`.
2. Stages the wrapped-block source into a local temp dir via `blocks bulk-load`.
3. Streams the staged files into `HISTORIC_MOUNT_PATH` inside the target pod via
   `tar | kubectl exec`.
4. Triggers a StatefulSet rollout so `BlockFileHistoricPlugin` re-scans and picks up the copied
   files.
5. Waits for the pod to come back `Ready`, then cleans up the staging dir.

### Verify

Once the pod is back `Ready`, confirm the BN sees the new blocks:

```bash
kubectl port-forward -n block-node svc/block-node-block-node-server 18082:40982 &
grpcurl -plaintext -emit-defaults \
  -import-path <path-to-protobuf-sources> \
  -proto block-node/api/node_service.proto \
  -d '{}' localhost:18082 org.hiero.block.api.BlockNodeService/serverStatus
```

Expected: `firstAvailableBlock` and `lastAvailableBlock` show real block numbers, not the
`UINT64_MAX` sentinel `18446744073709551615`.

### Resume

`blocks bulk-load` skips files already present in the destination. Re-running the script after a
partial copy (or after adding new wrapped-block zips to the source dir) copies only the new files.

### Corrupt-zip handling

`BlockFileHistoricPlugin` in 0.40.1+ auto-quarantines corrupt zips it finds during startup scan,
moving them to `<historic>/corrupted/…`. A single quarantine event can still trip the plugin's
first-vs-latest invariant check during that same startup (`IllegalStateException: First zipped
block number [0] cannot be greater than the latest zipped block number [-1]`); the second startup
usually succeeds because the corrupt file is out of the scan path. If it persists past two
restarts, see [Troubleshooting → invariant violation on start](#invariant-violation-on-start).

---

## Live push

After backfill, keep the SP-BN current by pushing each freshly-produced WRB from the CLI's live
wrap pipeline. This runs indefinitely alongside the CLI's normal `days live-sequential` loop.

```bash
nohup java -jar tools-*-all.jar --network <network> days live-sequential \
    -l metadata/listingsByDay \
    -o compressedDays \
    --wrap-output-dir wrappedBlocks \
    --address-book wrappedBlocks/addressBookHistory.json \
    --push-enabled \
    --push-bn-host <sp-bn-host> \
    --push-bn-port 40984 \
    --push-bn-status-port 40982 \
    > live-sequential.log 2>&1 &
```

### Push flags

|          Flag           |                                                 Meaning                                                  |
|-------------------------|----------------------------------------------------------------------------------------------------------|
| `--push-enabled`        | Enable push. Without this flag the wrap loop runs as-normal, no push traffic.                            |
| `--push-bn-host`        | SP-BN host (cluster-internal DNS, LoadBalancer external IP, or bind-mount address).                      |
| `--push-bn-port`        | SP-BN's `BlockStreamPublishService` port (publish stream). Standard: `40984`.                            |
| `--push-bn-status-port` | SP-BN's `BlockNodeService` port (status queries). Required in split-port deployments. Standard: `40982`. |
| `--push-queue-capacity` | Backpressure queue depth between the CLI wrap thread and the push worker. Default: `32`.                 |

In single-port deployments where all API services share one port, omit `--push-bn-status-port` — it
defaults to `--push-bn-port`.

### Startup behaviour

At startup, the tool queries the SP-BN's `serverStatus` on `--push-bn-status-port` to establish a
watermark. Startup log will show one of:

```
[live-sequential] Live push enabled: publish=<host>:<pub> status=<host>:<stat>
    queueCapacity=<N> BN is empty (will push from block 0)
```

or

```
[live-sequential] Live push enabled: publish=<host>:<pub> status=<host>:<stat>
    queueCapacity=<N> BN lastAvailableBlock=<N> (blocks <= this are skipped from push)
```

An **empty BN** advertises its watermark as `UINT64_MAX` (`18446744073709551615`) which the tool
recognises and pushes from block 0. This is the expected first-run behaviour against a fresh SP-BN.

If the tool fails at startup with `QueryFailedException: queryLastAvailableBlock failed against
<host>:<port>`, the message names the exact port that failed. Common causes:

- The BN pod isn't `Ready` yet — check `kubectl get pods -n block-node`.
- The status port is different from the publish port (split-port deployment) and
  `--push-bn-status-port` was not supplied.
- A network policy or firewall blocks the operator host from the status port.

### Steady-state signals

While pushing, the CLI logs a compact per-100-block summary line:

```
[live-sequential] Block <N> (<M> today, <rate> blocks/sec, queue=<n>/<capacity>)
```

On the SP-BN side, publisher-sourced blocks appear as:

```
Sending block persisted notification: block=<N> succeeded=true source=PUBLISHER
```

with matching `closeBlock  Completed blocks <N>` entries. If you see `Handler <id> is ending
mid-block <N>` bursts, the CLI is not emitting the mandatory `end_of_block` marker after each
block. Upgrade the CLI shadow jar — this was fixed in the same release that added
`--push-bn-status-port`.

---

## Serving Tier-1 nodes

Tier-1 BNs consume from the SP-BN through two existing mechanisms — no SP-BN-side configuration
change is required for either:

1. **Block backfill** — Tier-1 BNs' existing backfill plugin pulls blocks over the standard
   subscriber stream. Point their `blockNodeSourcesPath` config at the SP-BN endpoint.
2. **TSS retrieval** — Tier-1 BNs' `RosterBootstrapTssPlugin.queryPeerTssData()` calls the SP-BN's
   `serverStatus` API to pull TSS data from the roster the operator placed above.

Publish the SP-BN's reachable endpoint to Council Tier-1 operators. For a standard split-port
deployment publish:

- Host: cluster LoadBalancer external IP (or an operator-controlled DNS record fronting it).
- Publish port: `40984` (`BlockStreamPublishService`).
- Status port: `40982` (`BlockNodeService`).
- Subscriber port: `40980` (`BlockStreamSubscribeService`).
- Block-access port: `40981` (`BlockAccessService`).
- Health port: `40983` (`/healthz/readyz`, `/healthz/livez`).

See [network-ports-and-protocols.md](network-ports-and-protocols.md) for the authoritative port
table and firewall guidance.

---

## Monitoring

Roster-history-specific metrics (in addition to the standard BN publisher / verification metrics):

|               Metric                |                          Meaning                           |
|-------------------------------------|------------------------------------------------------------|
| `blocknode:roster_eras_loaded`      | Number of `[startBlock, endBlock]` eras loaded at startup. |
| `blocknode:roster_entries_loaded`   | Total `NodeAddress` entries summed across all eras.        |
| `blocknode:roster_load_duration_ms` | Startup load time in ms.                                   |

Sanity checks:

- `roster_eras_loaded` should equal the number of eras in your `rsa-bootstrap-roster.json`. A `1`
  after a converted history load usually means the loader fell back to single-book mode — check
  the file's actual contents.
- `roster_load_duration_ms` should be small (single-digit to low-double-digit ms) even for
  histories with tens of eras.

Recommended alerts (implementer choice on threshold):

- **BN tip stall** — `lastAvailableBlock` doesn't advance for `T` seconds while
  `days live-sequential --push-enabled` is running. Points at either a stuck publisher on the
  CLI side or a rejected stream on the BN side.
- **Verification failure "no address book"** — a WRB fell outside every era in the loaded
  history. Points at a history gap; extend or convert-and-replace the roster file.

---

## Troubleshooting

### Invariant violation on start

```
IllegalStateException: First zipped block number [0] cannot be greater than the latest zipped
block number [-1]
    at BlockFileHistoricPlugin.init(BlockFileHistoricPlugin.java:170)
```

Cause: `BlockFileHistoricPlugin` computed a "first block" (found at least one zip on disk) but
came back with `latest = -1` (no zip parseable as the highest). Common triggers:

- **Mixed on-disk layout** after a backfill that wrote both a `historic-plugin-bulk-load-state.json`
  and shard-nested `NNN/NNN/NNN/NNN/*.blk.zip` files. Different enumerators saw different views;
  the invariant check fails.
- **Corrupt zip** that got quarantined mid-scan on the first startup, leaving no readable zip in
  the shard that produced the "first" reading.
- **Ownership mismatch** — files copied as `root:root` while the container runs as UID `2000`;
  one enumerator can `list()` names, another can't `open()` the zips.

Recovery:

1. Restart the pod once. In 0.40.1+ the auto-quarantine typically self-heals on the second start.
2. If it doesn't, wipe the archive PVC (see [Wipe recipe](#wipe-recipe)) and re-run backfill from
   a *fresh* wrap output. Do not re-run backfill on top of a partial layout.

### Wipe recipe

Static-PV deployments must wipe file contents rather than deleting PVCs. Pattern using a
short-lived pod:

```bash
kubectl scale statefulset/block-node-block-node-server -n block-node --replicas=0
kubectl wait pod -l app.kubernetes.io/instance=block-node -n block-node \
  --for=delete --timeout=120s

# Apply a Pod manifest that mounts each PVC to wipe and runs `find … -delete` on its contents.
# Include archive/live/application-state for a full data reset; add plugins-storage as well
# whenever the image version has changed (the plugin JARs are version-specific and the
# resolve-plugins init container does NOT overwrite existing files).

kubectl apply -f wipe-pod.yaml
kubectl wait --for=jsonpath='{.status.phase}'=Succeeded pod/bn-wipe -n block-node --timeout=120s
kubectl logs -n block-node bn-wipe
kubectl delete pod -n block-node bn-wipe

kubectl scale statefulset/block-node-block-node-server -n block-node --replicas=1
kubectl rollout status statefulset/block-node-block-node-server -n block-node --timeout=300s
```

An example `wipe-pod.yaml` mounting `application-state`, `archive`, `live` at
`/app-state`, `/archive`, `/live` and running `find /app-state /archive /live -mindepth 1 -delete`
is the minimum shape — adjust PVC names to your release.

### `No BlockMessagingFacility provided` at startup

```
Exception in thread "main" java.lang.IllegalStateException: No BlockMessagingFacility provided
    at BlockNodeApp.<init>
```

Cause: the `plugins-storage` PVC has a JAR set left over from a previous image version, and the
`resolve-plugins` init container doesn't overwrite it. The main container's SPI scan then finds
mis-matched (or missing) plugin classes for the current image version.

Fix: wipe `plugins-storage` using the recipe above and restart. On version bumps, always include
`plugins-storage` in the wipe.

### PVs stuck `Released` after PVC delete

If you delete PVCs on a static-PV setup (`Retain` reclaim policy is default), the PVs keep the
stale `claimRef` and refuse to rebind to newly-recreated PVCs. Recovery:

```bash
kubectl patch pv <pv-name> --type=merge -p '{"spec":{"claimRef":null}}'
```

Repeat for each stranded PV, then the pending PVCs will bind and the pod can start. **Note that
the on-disk data is preserved.** If the goal was to wipe state, follow up with the
[wipe recipe](#wipe-recipe) after the PVs re-bind.

### Wrap tool: `Block counter out of sync with day block number`

The CLI's `days wrap` / `days live-sequential` persists its counter to
`wrappedBlocks/streamingMerkleTree.bin`. If the counter falls behind the day-metadata alignment
(missing days in `compressedDays/`, corrupt day archive, day file re-downloaded with different
content), the tool aborts before wrapping.

Fix: delete the stale state files and re-run:

```bash
rm wrappedBlocks/streamingMerkleTree.bin \
   wrappedBlocks/addressBookHistory.json \
   wrappedBlocks/nodeStakeHistory.json
# Re-run wrap from scratch (or from a known-good resume point)
```

If instead the message is `Day archives found without matching entries in day_blocks.json`,
regenerate metadata via `mirror extractDayBlock` before re-running wrap.

### Image / chart mismatch

Symptoms:

- Container exits with `/bin/bash: line 1: /opt/hiero/block-node/app-<X>/bin/app: No such file or directory`.
- `kubectl describe pod …` shows init containers on image tag `X` while main container is on tag `Y`.

Cause: `kubectl set image` bumped only the main container; the chart's init containers and startup
command still template version `X`. Reconciler on Helm-managed StatefulSets reverts the image
change on the next sync.

Fix: bump the whole chart, not just the image. On the operator host:

```bash
git -C ~/hiero-block-node fetch --tags
git -C ~/hiero-block-node checkout v<target>
helm dependency update ~/hiero-block-node/charts/block-node-server
helm upgrade block-node -n block-node --reuse-values \
  ~/hiero-block-node/charts/block-node-server
```

---

## Reference

### File paths

|                                Path                                 |       Owner        |                                       Purpose                                       |
|---------------------------------------------------------------------|--------------------|-------------------------------------------------------------------------------------|
| `/opt/hiero/block-node/application-state/rsa-bootstrap-roster.json` | BN (`app.state.*`) | RSA roster — single-book *or* `RangedAddressBookHistory` JSON; loader auto-detects. |
| `/opt/hiero/block-node/application-state/tss-parameters.bin`        | BN                 | TSS parameters; managed by the BN, do not hand-edit.                                |
| `/opt/hiero/block-node/data/historic/`                              | BN                 | On-disk historic archive; target of the bulk-load path.                             |
| `/mnt/wrb-operations/wrappedBlocks/tss-bootstrap-roster.json`       | CLI                | CLI-produced TSS bootstrap; copy into BN application-state.                         |
| `wrappedBlocks/addressBookHistory.json`                             | CLI                | CLI-produced address-book history; input to `convert-address-book-history`.         |

### Ports

Standard split-port deployment (Solo-provisioner defaults):

|  Port   |                   Service                    |
|---------|----------------------------------------------|
| `40980` | `BlockStreamSubscribeService`                |
| `40981` | `BlockAccessService`                         |
| `40982` | `BlockNodeService` (status)                  |
| `40983` | Health (`/healthz/readyz`, `/healthz/livez`) |
| `40984` | `BlockStreamPublishService`                  |

### Config keys

|                          Key                           |                               Default                               |                       Purpose                        |
|--------------------------------------------------------|---------------------------------------------------------------------|------------------------------------------------------|
| `app.state.rsaBootstrapFilePath`                       | `/opt/hiero/block-node/application-state/rsa-bootstrap-roster.json` | RSA / roster-history file path.                      |
| `roster.bootstrap.rsa.mirrorNodeBaseUrl`               | `""`                                                                | Mirror Node base URL; empty disables T6 maintenance. |
| `roster.bootstrap.rsa.mnInitialQueryIntervalMillis`    | `5000`                                                              | First-poll interval for MN maintenance.              |
| `roster.bootstrap.rsa.mnSubsequentQueryIntervalMillis` | `60000`                                                             | Steady-state poll interval for MN maintenance.       |

### Related docs

- Design: [sp-wrb-bn-design.md](../../design/wrb-streaming/sp-wrb-bn-design.md)
- CLI: [wrb-cli-runbook.md](wrb-cli-runbook.md)
- BN prep: [preparing-your-block-node-for-wrb-cutover.md](preparing-your-block-node-for-wrb-cutover.md)
- Reset / upgrade: [resetting-and-upgrading-the-block-node.md](resetting-and-upgrading-the-block-node.md)
- Ports: [network-ports-and-protocols.md](network-ports-and-protocols.md)
- Glossary: [glossary.md](../glossary.md) — WRB, TSS, roster terminology.
