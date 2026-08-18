# Resetting and Upgrading the Block Node

This guide explains when to reset or upgrade a deployed Block Node, why each operation matters, and how to perform both safely on a Kubernetes deployment.

It assumes the Block Node was installed using the standard Helm chart, either via the [Manual Single-Node Kubernetes Deployment](./single-node-k8s-deployment.md) guide or the [Solo Provisioner Single-Node Kubernetes Deployment](./solo-weaver-single-node-k8s-deployment.md) guide.

## Overview

Reset and upgrade are two distinct day-two operations that are often confused:

- **Upgrade** changes the running Block Node version (image tag and Helm chart version) without discarding the local block storage or state. Pre-existing live and historic blocks remain on disk; the new version resumes from where the previous one left off.
- **Reset** wipes the Block Node's local block data (both live blocks and historic blocks), clears the node application state details (e.g. rosters), and starts the node fresh. A reset is destructive: Consensus Nodes only keep a minimal recent buffer and cannot serve the wiped history, so the lost data must be backfilled from another Block Node that holds the relevant range (typically a Tier 1 archive node). For a mature network — Hedera mainnet, for example — that [backfill](../glossary.md#backfill) can take a very long time (potentially weeks), proportional to the total block history.

The two operations can be chained. **[Solo Provisioner](../glossary.md#solo-provisioner) is the recommended path**: `sudo solo-provisioner block node upgrade --with-reset` performs the reset and the upgrade in a single managed transaction (the `--with-reset` flag wipes the block node data directories; PVs and PVCs are preserved). For manual Taskfile-managed deployments, `task reset-upgrade` is the equivalent. Use the chained operation when you need to discard data and move forward; use a plain upgrade (`sudo solo-provisioner block node upgrade` or `task helm-upgrade`) for a clean version bump.

> **Production Block Nodes should rarely, if ever, be reset under normal circumstances.** Treat reset as a recovery procedure for corruption, version-incompatibility, or network changes — not as routine maintenance. For high-value Block Nodes, maintain an offline backup of the live and archive PVC contents (updated daily where possible) so a corrupted node can be restored from snapshot instead of resyncing from another Block Node.

## Prerequisites

Before you begin, ensure you have:

- A running Block Node deployment on Kubernetes installed via the standard Helm chart. See
  - [Manual Single-Node Kubernetes Deployment](./single-node-k8s-deployment.md)
  - [Solo Provisioner Single-Node Kubernetes Deployment](./solo-weaver-single-node-k8s-deployment.md).
- Shell access to the host where the operator's `kubectl`, `helm`, and `task` tooling is configured against the target cluster.
- The Node Operations Taskfile from the repository: [`tools-and-tests/scripts/node-operations/Taskfile.yml`](https://github.com/hiero-ledger/hiero-block-node/blob/main/tools-and-tests/scripts/node-operations/Taskfile.yml). The `task` CLI loads its `.env` file from the same directory.
- A populated `.env` file in the Taskfile directory with at minimum:
  - `RELEASE` - Helm release name used at install time.
  - `NAMESPACE` - Kubernetes namespace the Block Node was installed into.
  - `VERSION` - the chart version (and image appVersion) to upgrade to.
  - `POD` - Block Node pod name (only needed for `reset-file-store` / `reset-upgrade`).
- A maintenance window if the Block Node is serving downstream Mirror Nodes or other subscribers. Upgrade restarts the pod and briefly interrupts subscribe streams; reset additionally interrupts ingestion until the new pod re-establishes its publisher connection.

## When and why to upgrade

Upgrade the Block Node when any of the following is true:

- A new Block Node release contains fixes or features you need - security, correctness, plugin behaviour, or operational improvements. Track releases at [hiero-block-node releases](https://github.com/hiero-ledger/hiero-block-node/releases).
- The Helm chart shape has changed - for example, new required values, renamed configuration keys, or added persistent volume mounts. The chart and the Block Node image versions are released together and share an `appVersion`.
- Compatibility with the rest of the Hiero network requires it. A single Block Node release is intended to span all stages of the records-to-block-streams [cutover](../glossary.md#cutover-release), but if a future release introduces a hard compatibility break with the publishing Consensus Nodes or the surrounding ecosystem, the upgrade becomes mandatory. Refer to [Cutover-Process](../Cutover-Process.md) for the network milestone view.

**Why it matters:** An upgrade preserves local block data and state, so the Block Node continues to serve historical block-range requests immediately after the new pod becomes ready. There is no backfill window unless the upgrade itself fails.

## When and why to reset

Reset the Block Node only when there is a concrete reason to discard local data. Acceptable reasons:

- **Corrupt or inconsistent on-disk state** - the pod fails to start, repeatedly crashes after reading existing data, or `serverStatus` returns block-range values that disagree with the Mirror Nodes or other Block Nodes the operator can compare against.
- **Switching networks** - moving an existing Block Node deployment from one network (for example `previewnet`) to another (for example `testnet`). The block numbering and address book differ across networks; the existing data will not be valid for the new network. Reset is required, not optional.
- **Recovery from a bad version in dev or test environments** - rolling back from a development build that wrote incompatible data to disk. In `testnet` or `mainnet`, encountering this would represent a major process failure upstream and should not occur under normal release management.
- **Cleaning a dev or test deployment** before reusing the cluster for a fresh integration run.

**Why it matters:** Reset is destructive. The Block Node wipes its live and historic block stores on disk and restarts; the pod will report an empty range (`firstAvailableBlock = lastAvailableBlock = uint64_max`) and must be backfilled from another Block Node that holds the relevant history (Consensus Nodes cannot supply it — they only retain a minimal recent buffer). Mirror Nodes pointed at the reset Block Node will see `NOT_AVAILABLE` until enough blocks have been backfilled to satisfy their `start_block_number`. On a mature network, full backfill can take days or weeks.

> **Caution:** Reset cannot be undone from inside the Block Node. If you need a recoverable snapshot of the data before resetting, copy the contents of the live and archive PVCs to off-cluster storage first.

## Upgrading the Block Node

> If your upgrade involves enabling or disabling plugins, see [Plugin Management](../configuration.md#plugin-management) in the configuration reference.

### Step 1: Confirm the current state is healthy

```bash
kubectl get pods -n "$NAMESPACE"
kubectl get statefulset -n "$NAMESPACE"
```

- **Expected:** the Block Node pod is `Running` and ready. The StatefulSet's `READY` count equals its `DESIRED` count.
- **If the pod is unhealthy:** do not upgrade. Investigate first with `kubectl logs` and `kubectl describe pod`. Upgrading an unhealthy pod typically converts a recoverable problem into a stuck rollout.

Capture the current version for rollback reference:

```bash
helm list -n "$NAMESPACE" | grep "$RELEASE"
```

Capture the current block range:

```bash
grpcurl -plaintext -emit-defaults \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  "$BLOCK_NODE_HOST:40982" \
  org.hiero.block.api.BlockNodeService/serverStatus
```

Record `firstAvailableBlock` and `lastAvailableBlock` - both should be sensible block numbers (not `uint64_max`) if the Block Node has been ingesting. After the upgrade these values must continue from where they left off.

### Step 2: Bump the target version

Edit the `.env` file used by the Node Operations Taskfile and set `VERSION` to the target chart version. The chart, the image, and the protobuf bundle all share this version. Confirm the target is available on the [releases page](https://github.com/hiero-ledger/hiero-block-node/releases) before proceeding.

```bash
# example .env line
VERSION=0.34.0
```

### Step 3: Run the upgrade

```bash
task helm-upgrade
```

This runs `helm upgrade $RELEASE oci://ghcr.io/hiero-ledger/hiero-block-node/block-node-server --version $VERSION -n $NAMESPACE --install --values values-override/bare-metal-values.yaml` followed by `kubectl get all -n $NAMESPACE`. The `--install` flag makes the operation idempotent - if for any reason the release is missing, it is installed; otherwise it is upgraded in place.

**The StatefulSet rolls the pod**: the existing pod terminates, a new pod with the updated image starts, and the PVCs that hold live and historic block data are remounted unchanged.

### Step 4: Verify post-upgrade health

```bash
kubectl get pods -n "$NAMESPACE" -w
```

Wait until the new pod reports `Running` with all containers ready, then `Ctrl-C` the watch.

Re-run `serverStatus`:

```bash
grpcurl -plaintext -emit-defaults \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  "$BLOCK_NODE_HOST:40982" \
  org.hiero.block.api.BlockNodeService/serverStatus
```

- **Expected:** the same `firstAvailableBlock` you recorded before the upgrade. `lastAvailableBlock` should equal or exceed the previous value and continue to advance as Consensus Nodes publish new blocks.
- **If `firstAvailableBlock` regressed to `uint64_max`:** the upgrade lost block data. Stop and investigate before resetting or moving forward - this should not happen on a chart-only upgrade.

Scrape subscriber metrics to confirm downstream consumers reconnect:

```bash
curl -s "http://$BLOCK_NODE_HOST:16007/metrics" | grep blocknode_subscriber
```

- **Expected:** `blocknode_subscriber_open_connections` rises back to its pre-upgrade level as Mirror Nodes and other subscribers re-establish their subscriptions. `blocknode_subscriber_errors_total` may tick up by the number of subscribers that were dropped during the rollout - this is expected.

### Step 5 (if rollback is required): revert to the previous version

> **Note:** The procedure to revert to a previous chart version depends on how the Block Node was installed. Follow the path below that matches your install method.

#### Mid-upgrade failures

The auto-recovery behaviour for a failed mid-upgrade transaction depends on how the upgrade was invoked:

- **Solo Provisioner-managed upgrades** are invoked with Helm's `--atomic` flag, so Helm itself reverts a failed transaction in place. The Provisioner additionally exposes `--rollback-on-error`, which unwinds any completed workflow steps in reverse — including downgrading the chart back to the previously installed version and removing PV/PVCs that the failed migration just created. No manual intervention is required.
- **`task helm-upgrade` does not pass `--atomic`.** If `helm upgrade` fails partway, the release can be left in a partially applied state. Investigate with `helm status "$RELEASE" -n "$NAMESPACE"`, then either run `helm rollback` to the previous revision (subject to the Path B caveats below) or re-run `task helm-upgrade` after correcting the underlying issue.

If the upgrade returned cleanly but the new version turned out to be unhealthy in steady state, follow the path below that matches your deployment.

#### Path A: Solo Provisioner-managed deployments

The Solo Provisioner **rejects in-place chart-version downgrade**. Running `solo-provisioner block node upgrade --chart-version <lower>` returns:

> `block node chart version cannot be downgraded from X to Y; version downgrade is not supported.`

**Do not run `helm rollback` or `helm upgrade --version <lower>` directly against a Solo Provisioner-managed release.** The Provisioner keeps its own record of the deployed chart version in a state file. A direct Helm call changes the cluster but not the state file, so the next `solo-provisioner` command operates on a stale view. Likely symptoms: legitimate upgrades refused, migrations that should re-run are skipped, values rendered for the wrong version. Reconciling the state file with the cluster afterwards is manual.

The supported procedure is uninstall and reinstall of the previous version:

```bash
sudo solo-provisioner block node uninstall
sudo solo-provisioner block node install -p <profile>
```

`solo-provisioner block node uninstall` deletes the StatefulSet but leaves the PVs and PVCs intact. A [Local Full History](../glossary.md#local-full-history-lfh) (LFH) Block Node retains its data on disk, so the subsequent reinstall does not trigger a full backfill. Ensure the Provisioner is configured to install the previous chart version (consult the Solo Provisioner documentation for the version-selection flag or state-file format used by your release).

#### Path B: Manual Taskfile-managed deployments

If you installed via `task helm-release` rather than the Solo Provisioner, you can use Helm's release-history rollback directly:

```bash
helm history "$RELEASE" -n "$NAMESPACE"
helm rollback "$RELEASE" <previous-revision> -n "$NAMESPACE"
```

The Helm release manifest reverts to the named revision. The PVCs are untouched, so existing block data remains available to the rolled-back pod.

> **Caveat - migrations are forward-only.** `helm rollback` reverts the Helm release manifest only. Side effects that the higher version introduced — new PVCs, modified StatefulSet shape, ConfigMap changes — are not reversed. If the upgrade between the two revisions crossed a migration boundary, the rolled-back deployment may end up inconsistent (chart says version N, the disk and resource layout say version N+1). In that case, treat the situation as a full reset: `task clear-release` followed by `task helm-release` with `VERSION` set to the previous chart version. This drops block data, so confirm that's acceptable before proceeding.

## Resetting the Block Node

There are two reset paths, depending on whether you also want to bump the version.

### Path A: Reset on the same version

Use this when the on-disk data is corrupt and you simply want to start the current version with a clean store.

```bash
task reset-file-store
```

This execs into the pod and removes the live and historic block-store contents managed by the deployed Block Node plugins, then deletes the pod. The StatefulSet recreates the pod and the new pod starts with empty data directories. PVCs are preserved (the data is wiped inside the PVC, not by destroying the PVC). The exact paths cleared depend on which storage plugins are enabled in the running chart; see [`tools-and-tests/scripts/node-operations/Taskfile.yml`](https://github.com/hiero-ledger/hiero-block-node/blob/main/tools-and-tests/scripts/node-operations/Taskfile.yml) for the canonical commands run by `task reset-file-store`.

### Path B: Reset and upgrade in one step

Use this when you want to discard data and move to a new version at the same time - for example, when switching networks or recovering from a bad version.

```bash
task reset-upgrade
```

Internally this chains `reset-file-store` and `helm-upgrade`. The data store is cleared first, then the upgrade runs and the new image starts on the clean PVC.

### Verification after a reset

```bash
kubectl get pods -n "$NAMESPACE" -w
```

Wait until the recreated pod is `Running` with all containers ready.

```bash
grpcurl -plaintext -emit-defaults \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  "$BLOCK_NODE_HOST:40982" \
  org.hiero.block.api.BlockNodeService/serverStatus
```

- **Expected immediately after reset:**

  ```json
  {
    "firstAvailableBlock": "18446744073709551615",
    "lastAvailableBlock": "18446744073709551615",
    "onlyLatestState": false
  }
  ```

  Both values at `uint64_max` confirm an empty Block Node. The pod is ready to re-ingest blocks from a Consensus Node or backfill from an upstream Block Node.

- **Expected after ingestion resumes:** `firstAvailableBlock` populates with the first block the reset Block Node ingests (or backfills) and `lastAvailableBlock` advances as Consensus Nodes publish.

## Full uninstall (when reset is not enough)

### Path A: Solo Provisioner-managed deployments

`sudo solo-provisioner block node uninstall` removes the StatefulSet but leaves PVs and PVCs intact — block data is preserved on disk. Note that `reconfigure --with-reset` is an exception to this rule: it wipes data inside volumes and also deletes the PVs and PVCs. If you need to remove PVs and PVCs without a reconfigure, the only supported path is a full cluster teardown:

```bash
sudo solo-provisioner kube cluster uninstall
```

> **Caution:** `kube cluster uninstall` tears down the entire Kubernetes cluster and all resources within it. Use only if you intend to decommission the cluster entirely, not just the Block Node.

### Path B: Manual Taskfile-managed deployments

If you need to remove the Block Node entirely — PVCs, PVs, namespace, the lot - use:

```bash
task clear-release
```

This runs `helm uninstall $RELEASE -n $NAMESPACE`, deletes the live, logging, and archive PVCs and PVs, and deletes the namespace. It is the inverse of `task helm-release` and prepares the cluster for a fresh install rather than a reset.

> **Caution:** `clear-release` is more destructive than `reset-upgrade`. It removes the Kubernetes namespace and all resources within it, including any non–Block-Node resources you may have placed in the same namespace. Confirm the namespace is dedicated to the Block Node before running.

## Troubleshooting

|                                                                                              Symptom                                                                                               |                                           Likely cause                                            |                                                                                                          Resolution                                                                                                           |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| New pod stuck in `ContainerCreating` after `task helm-upgrade`                                                                                                                                     | Image pull failure for the new `VERSION`.                                                         | `kubectl describe pod -n "$NAMESPACE"` - look for `ImagePullBackOff`. Verify the version tag exists at `ghcr.io/hiero-ledger/hiero-block-node` and any pull secrets are valid.                                                |
| `helm upgrade` fails with `UPGRADE FAILED: ... has no deployed releases`                                                                                                                           | The release name is wrong, or the release was previously fully uninstalled.                       | Confirm `$RELEASE` and `$NAMESPACE` in `.env` match an installed release: `helm list -n "$NAMESPACE"`. If the previous release was cleared, use `task helm-release` to do a fresh install.                                    |
| `firstAvailableBlock` regressed to `uint64_max` after an upgrade you did not run as a reset                                                                                                        | The new pod is reading from a different PVC, or the PVC was inadvertently recreated.              | `kubectl describe statefulset -n "$NAMESPACE"` - check that `volumeClaimTemplates` and bound PVCs match the names from the install. If PVCs were destroyed, the data is gone; treat as a reset and proceed with re-ingestion. |
| `kubectl exec` inside `reset-file-store` fails with `error: unable to upgrade connection`                                                                                                          | The pod is restarting or not ready when the task runs.                                            | Wait until the pod is `Running` and ready, then re-run `task reset-file-store`.                                                                                                                                               |
| `task reset-upgrade` succeeded but Mirror Nodes still see `NOT_AVAILABLE`                                                                                                                          | Expected - the reset Block Node has no blocks yet.                                                | Wait for ingestion or backfill to populate the block range. Mirror Nodes will reconnect automatically once `start_block_number` is within the available range.                                                                |
| `helm rollback` fails with `release: not found`                                                                                                                                                    | Helm history was pruned, or the rollback target revision does not exist.                          | List available revisions with `helm history "$RELEASE" -n "$NAMESPACE"`. If no eligible target remains, redeploy by setting `VERSION` to the previous chart version and running `task helm-upgrade`.                          |
| `solo-provisioner block node upgrade --chart-version <lower>` returns `block node chart version cannot be downgraded from X to Y`                                                                  | Expected — the Provisioner rejects in-place downgrades.                                           | Use the supported uninstall + reinstall path from [Step 5 Path A](#path-a-solo-provisioner-managed-deployments).                                                                                                              |
| After a direct `helm rollback` on a Solo Provisioner-managed release, the next `solo-provisioner` command refuses a legitimate upgrade, skips a migration, or renders values for the wrong version | Provisioner state file is out of sync with the cluster — direct `helm` calls do not update it.    | Reconcile manually: re-align the Provisioner's recorded chart version with the actual cluster state, or follow the uninstall + reinstall path from [Step 5 Path A](#path-a-solo-provisioner-managed-deployments).             |
| After `helm rollback` on a manual deployment, the pod fails to start or the StatefulSet appears to have extra/missing PVCs compared to the rolled-back chart                                       | Forward-only migrations were applied during the upgrade and were not reversed by `helm rollback`. | Treat as a full reset: `task clear-release` then `task helm-release` with the previous `VERSION`. Confirm block data loss is acceptable first.                                                                                |
| Pod is `Running` but `serverStatus` returns `connection refused` from outside the cluster                                                                                                          | Service or LoadBalancer was recreated with a different external address.                          | `kubectl get svc -n "$NAMESPACE"` - confirm the external IP / port mapping; update downstream consumers if it changed.                                                                                                        |
| After a cross-network reset, the Block Node refuses publisher connections from the new network's Consensus Nodes                                                                                   | Stale address-book or network configuration in the new chart values.                              | Confirm the Helm values for the new network are applied (`--values values-override/<new-network>.yaml`). Run a `task helm-upgrade` after correcting the values.                                                               |

---
