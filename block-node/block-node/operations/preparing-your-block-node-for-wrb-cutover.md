# Preparing Your Block Node for WRB Cutover

This guide walks a Tier 1 Block Node operator through the steps required to prepare a deployed Block Node for the WRB (Wrapped Record Block) streaming cutover, including pre-upgrade checks that apply to every upgrade and the additional steps specific to the WRB cutover release.

It assumes the Block Node is installed using the Solo Provisioner, either on a bare-metal server (see [Bare Metal Single Node Kubernetes Deployment](./single-node-k8s-deployment.md)) or a GCP VM (see [Virtual Machine Single Node Kubernetes Deployment](./solo-weaver-single-node-k8s-deployment.md)).

The steps are organized in two sections. The **common pre-upgrade checks** apply before every upgrade, regardless of release. The **release-specific checks** contain the additional steps the WRB cutover requires; future network releases will appear as additional subsections without changing the common section.

---

## Common pre-upgrade checks

Complete all steps in this section before every upgrade, regardless of release.

### Confirm Block Node health

Before upgrading, confirm the Block Node is healthy. Do not upgrade an unhealthy node — an in-progress failure becomes a stuck rollout.

1. List all pods and confirm the Block Node pod is `Running` with all containers ready:

   ```bash
   kubectl get pods -A
   kubectl -n block-node get pods,sts,svc
   ```

   - **Expected:** the `block-node-block-node-server-0` pod shows `1/1` ready and `Running`.
   - **If the pod shows `CrashLoopBackOff`, `ImagePullBackOff`, or `0/1` ready:** investigate with `kubectl -n block-node logs <BN_POD>` and `kubectl -n block-node describe pod <BN_POD>` before proceeding.
2. Record the current block range so you can confirm it does not regress after the upgrade. Retrieve the pod name first:

   ```bash
   BN_POD=$(kubectl -n block-node get pod \
     -l app.kubernetes.io/name=block-node \
     -o jsonpath='{.items[0].metadata.name}')
   ```

   Then call `serverStatus`:

   ```bash
   grpcurl -plaintext -emit-defaults \
     -import-path ~/bn-proto \
     -proto block-node/api/node_service.proto \
     -d '{}' \
     "$BLOCK_NODE_HOST:40840" \
     org.hiero.block.api.BlockNodeService/serverStatus
   ```

   Record `firstAvailableBlock` and `lastAvailableBlock`. Both should be sensible block numbers — not `18446744073709551615` (the sentinel for "no blocks yet") — if the Block Node has been ingesting. For instructions on downloading the protobuf bundle into `~/bn-proto`, see [Connecting a Mirror Node to a Block Node](./connecting-a-mirror-node-to-a-block-node.md).

3. Confirm Alloy telemetry is shipping (if configured):

   ```bash
   kubectl -n grafana-alloy get pods
   ```

   All Alloy pods should be `Running`. A non-running Alloy pod is not a blocker for the upgrade but should be investigated afterwards.

### Validate hardware readiness

Run the Solo Provisioner hardware preflight check before the upgrade. This validates CPU core count, RAM, disk, OS version, and network-connectivity requirements for the target profile.

```bash
sudo solo-provisioner block node check --profile=mainnet
```

- **Expected:** each step shows a green checkmark (✅) and "success".
- **If a step fails:** it reports the specific requirement not met. For hardware minimums, see [Block Node Hardware Specifications](./block-node-hardware-specifications.md). The preflight counts physical CPU cores, not vCPUs.

> Note: The `--profile=mainnet` flag is required. Omitting it returns `profile flag is required`.

### Validate required directories and free space

The Block Node uses five persistent volumes. Confirm each is mounted and has adequate free space before upgrading. The `solo-provisioner block node check` preflight validates storage requirements for the configured profile.

|     Volume     |                                      Default mount path                                       | Minimum free space |                                         Purpose                                         |
|----------------|-----------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------------------|
| `live`         | `blockNode.persistence.live.mountPath` (default `/opt/hiero/block-node/data/live`)            | 6 TB               | Recent block stream and live state                                                      |
| `archive`      | `blockNode.persistence.archive.mountPath` (default `/opt/hiero/block-node/data/historic`)     | 90 TB              | Compressed historic block archive                                                       |
| `verification` | `blockNode.persistence.verification.mountPath` (default `/opt/hiero/block-node/verification`) | 50 GB              | Block hash state and verification data                                                  |
| `logging`      | `blockNode.persistence.logging.mountPath` (default `/opt/hiero/block-node/logs`)              | 100 GB             | Application logs                                                                        |
| `plugins`      | `blockNode.persistence.plugins`                                                               | —                  | Plugin JARs; always mounted but may contain no JARs until plugins are explicitly loaded |

> Note: The exact mount paths depend on your Helm values. Run `helm -n block-node get values <release-name>` to inspect your installation's overrides.
>
> Note: In the next Block Node version, `blockNode.persistence.verification.mountPath` will be replaced by `blockNode.persistence.applicationState.mountPath`. Check the Helm chart release notes for your target version before running volume-size checks.

Confirm all volumes are mounted and visible inside the pod:

```bash
kubectl -n block-node exec $BN_POD -c block-node-server -- df -h \
  /opt/hiero/block-node/data/live \
  /opt/hiero/block-node/data/historic \
  /opt/hiero/block-node/verification \
  /opt/hiero/block-node/logs
```

- **Expected:** each path reports a filesystem with non-zero size and adequate free space.
- **If any path is missing:** the volume is not mounted. Run `kubectl -n block-node describe pod $BN_POD` and look for mount errors.

### Confirm provisioner version

Upgrade Solo Provisioner to the latest release before upgrading the Block Node. Your Hashgraph PoC will confirm the supported provisioner version for the cohort.

1. Check the installed version:

   ```bash
   sudo solo-provisioner -v
   ```

   - **Expected output** (version varies by release):

     ```text
     {"version":"0.19.0","commit":"<git-sha>","goversion":"go1.26.0"}
     ```
2. If the version is behind the target, upgrade:

   ```bash
   curl -sSL https://raw.githubusercontent.com/hashgraph/solo-weaver/main/install.sh | bash
   sudo solo-provisioner -v
   ```

   The install script downloads the latest GA release for your architecture, verifies the SHA256 checksum, and replaces the existing binary.

### Back up operator-side artifacts

Before the upgrade, confirm you have current copies of the following files stored off the BN host. These cannot be reproduced from upstream if the host is lost.

|          Artifact           |                                                  Location on host                                                   |                                              Why it is irreplaceable                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| `block-node-values.yaml`    | `/etc/solo-provisioner/block-node-values.yaml`                                                                      | Cohort-specific Helm overlay generated once at handoff                                                             |
| `rsa-bootstrap-roster.json` | Default: `/opt/hiero/block-node/node/rsa-bootstrap-roster.json` (configurable via `app.state.rsaBootstrapFilePath`) | RSA public-key roster for WRB proof verification; only needed when not using Mirror Node auto-fetch for the roster |

---

## Release-specific checks

Complete the subsection below that matches the upgrade you are preparing for. Future releases will appear here as additional subsections. If no matching subsection exists for your target release, only the common checks above are required.

---

### WRB streaming cutover prep

**Applies to:** the Consensus Node release that activates Wrapped Record Block (WRB) streaming — currently scheduled for CN release 0.75.0. The exact release may change if release testing surfaces a blocker; your Hashgraph PoC will confirm the target release before the maintenance window.

For the full network cutover timeline — phases, CN-side WRB catch-up, TSS ceremony, and Jumpstart Data — see [Cutover Process and Timeline](../Cutover-Process.md).

**What is changing:** from the cutover release onwards, Consensus Nodes begin producing Wrapped Recordfile Blocks with aggregated RSA signature Block Proofs. The production of Record files uploaded to S3 storage will continue until the cutover to TSS and Block Streams in a later release. Any preview blocks stored by the Block Node before the cutover are invalid and must be discarded before the BN can receive and store authoritative WRB history. Do not skip the reset step even if the BN appears to be functioning normally.

> **Note:** Before beginning the WRB cutover steps, confirm that the Consensus Nodes peered with this Block Node are no longer streaming preview blocks. Ingesting blocks from a CN that is still in preview mode will store invalid data that requires another reset to clear.

Complete the [common pre-upgrade checks](#common-pre-upgrade-checks) first, then continue here.

#### Clear the block store (preview-blocks reset)

> **Caution:** This operation is destructive and cannot be undone. It scales the StatefulSet to 0, clears all files from the `live`, `archive`, `verification`, and `logging` storage directories, then scales back up to 1. All block data on this node is lost. Do not run this step until your Hashgraph PoC has confirmed the maintenance window is open and your off-host artifact backups are current.

```bash
sudo solo-provisioner block node reset --profile=mainnet
```

- **Expected output:**

  ```text
  Ensuring weaver service account (weaver:2500)
  Preflight Checks
  Scaling down Block Node
  Clearing Block Node storage
  Scaling up Block Node
  Waiting for Block Node to be ready

  Completed successfully
  ```

Wait until the pod reaches `1/1 Running`, then confirm the store is empty:

```bash
grpcurl -plaintext -emit-defaults \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  "$BLOCK_NODE_HOST:40840" \
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

  Both values at `18446744073709551615` confirm the store is empty and the Block Node is ready to backfill.

If the reset fails, run the following to diagnose the issue and share the output with your Hashgraph PoC:

```bash
sudo head /opt/solo/weaver/logs/solo-provisioner.log
kubectl -n block-node describe pod $BN_POD
kubectl -n block-node logs $BN_POD -c block-node-server --tail=200
```

#### Provision the RSA bootstrap roster

The `roster-bootstrap-rsa` plugin must be included in your Block Node's plugin list and have the RSA public-key roster available at startup to verify `SignedRecordFileProof` items in incoming WRBs.

**Enable the plugin**

Add `roster-bootstrap-rsa` to your plugin list in `block-node-values.yaml`. Append it to your existing `plugins.names` value — the example below shows the full plugin list used for LFH nodes on previewnet, provided for reference:

```yaml
plugins:
  names: facility-messaging,block-access-service,health,server-status,stream-publisher,stream-subscriber,verification,blocks-file-historic,blocks-file-recent,backfill,roster-bootstrap-rsa,roster-bootstrap-tss
```

**Mirror Node auto-fetch (mainnet cohort default)**

When `ROSTER_BOOTSTRAP_RSA_MIRROR_NODE_BASE_URL` is set in your `block-node-values.yaml`, the plugin fetches the roster from the Mirror Node REST API at startup and caches it locally. After `block node reset`, the cached copy is cleared along with the rest of the storage directories and is automatically re-fetched on the next pod start — no manual intervention is needed. Before the cutover window, confirm outbound connectivity to the Mirror Node is available from the BN host.

Set the URL in your Helm values overlay:

```yaml
config:
  ROSTER_BOOTSTRAP_RSA_MIRROR_NODE_BASE_URL: "https://mainnet-public.mirrornode.hedera.com"
```

Mirror Node base URLs by network:

|  Network   |                      URL                       |
|------------|------------------------------------------------|
| Mainnet    | `https://mainnet-public.mirrornode.hedera.com` |
| Testnet    | `https://testnet.mirrornode.hedera.com`        |
| Previewnet | `https://previewnet.mirrornode.hedera.com`     |

> Note: If the Mirror Node is unreachable, the plugin polls indefinitely — every 5 seconds until the first roster is fetched, then every 60 seconds for periodic refresh. Both intervals are configurable. WRB proof verification is non-functional until the roster is available.

**Peer Block Node query**

All Tier 1 Block Nodes should have at least one peer Block Node source configured; more than one is recommended for redundancy. When `roster.bootstrap.rsa.blockNodeSourcesPath` is set, the plugin queries configured peer Block Nodes via gRPC to retrieve the roster. This runs concurrently with the Mirror Node query; whichever responds first provides the initial roster. The peer BN query follows the same two-phase polling schedule (5-second initial interval, 60-second subsequent interval, both configurable).

This is the **same file** referenced by `BACKFILL_BLOCK_NODE_SOURCES_PATH` — configuring it once serves both the roster-bootstrap-rsa plugin and the backfill plugin. Set the path in your Helm values overlay:

```yaml
config:
  BACKFILL_BLOCK_NODE_SOURCES_PATH: "/opt/hiero/block-node/config/block-node-sources.json"
```

The file format is a JSON object with a `nodes` array:

```json
{
  "nodes": [
    { "address": "peer1.example.com", "port": 40840, "priority": 1, "name": "peer-bn-1" },
    { "address": "peer2.example.com", "port": 40840, "priority": 2, "name": "peer-bn-2" }
  ]
}
```

Configure this file via your Helm chart's ConfigMap or values override mechanism. Use the endpoints provided by your Hashgraph PoC.

**Manual file (alternative)**

If `ROSTER_BOOTSTRAP_RSA_MIRROR_NODE_BASE_URL` is not configured, the plugin reads the roster from `app.state.rsaBootstrapFilePath` (default: `/opt/hiero/block-node/node/rsa-bootstrap-roster.json`). The file is delivered as part of the cohort package by your Hashgraph PoC. After `block node reset`, confirm the file is still present and non-empty:

```bash
kubectl -n block-node exec $BN_POD -c block-node-server -- \
  ls -lh /opt/hiero/block-node/node/rsa-bootstrap-roster.json
```

- **If the file is missing after reset:** re-deliver it from your off-host backup using the mechanism in your cohort's `block-node-values.yaml`.

In either case, confirm the roster loaded cleanly after the pod starts by querying `serverStatusDetail` — the response should contain a non-empty `rosterHash` field:

```bash
grpcurl -plaintext -emit-defaults \
  -import-path ~/bn-proto \
  -proto block-node/api/node_service.proto \
  -d '{}' \
  "$BLOCK_NODE_HOST:40840" \
  org.hiero.block.api.BlockNodeService/serverStatusDetail
```

- **Expected:** the response includes a non-empty `rosterHash` field, confirming the RSA roster is loaded and active.

#### Configure backfill sources (if required)

After the block store reset, the BN backfills WRB history automatically as long as other Block Nodes on the network already hold the relevant block range. All Tier 1 operators should have at least one Block Node source configured for both backfill and peer roster queries. **Most operators do not need to manually configure a specific backfill source** — once peer Block Nodes are advertising history on the network, the backfill plugin discovers them through the normal backfill path.

**Enable greedy backfill**

Hashgraph recommends enabling greedy backfill on all Tier 1 Block Nodes for the WRB cutover. With greedy backfill enabled, the BN proactively retrieves blocks beyond the latest acknowledged block, preventing the node from falling too far behind during the initial catch-up period.

In your Helm values overlay, set `BACKFILL_GREEDY` to `"true"`, apply the change, and confirm:

```bash
helm -n block-node upgrade <release-name> <chart> -f block-node-values.yaml
helm -n block-node get values <release-name> | grep BACKFILL_GREEDY
```

- **Expected:** `BACKFILL_GREEDY: "true"`

**Edge case — bootstrapping from genesis when no public BN holds the history yet:**

During the initial mainnet WRB cutover, the wrapped record block history may not yet be available from public Block Nodes. In this case, a special-purpose Block Node holding the offline-wrapped WRBs serves as a temporary backfill source. Operators who need access to this node will receive the endpoint and connection details through a separate operator communication channel — it is not published in this document.

If you are directed by your Hashgraph PoC to configure a specific backfill source:

1. Update your Helm values overlay with the backfill source path, `BLOCK_NODE_EARLIEST_MANAGED_BLOCK` set to `"0"` (so the BN manages from genesis), a `startBlock` of `"0"`, and a `fetchBatchSize` of `"100"` for faster throughput. Apply the change:

   ```yaml
   config:
     BACKFILL_BLOCK_NODE_SOURCES_PATH: "/opt/hiero/block-node/config/block-node-sources.json"
     BLOCK_NODE_EARLIEST_MANAGED_BLOCK: "0"
     BACKFILL_START_BLOCK: "0"
     BACKFILL_FETCH_BATCH_SIZE: "100"
   ```

   ```bash
   helm -n block-node upgrade <release-name> <chart> -f block-node-values.yaml
   helm -n block-node get values <release-name> | grep -i backfill
   ```
2. The `block-node-sources.json` file format is a JSON object with a `nodes` array. Configure the file via your Helm chart's ConfigMap or values override mechanism. Use the hostname and port provided by your Hashgraph PoC:

   ```json
   {
     "nodes": [
       {
         "address": "<host-provided-by-hashgraph-poc>",
         "port": 40840,
         "priority": 1
       }
     ]
   }
   ```

   > Note: Even when backfilling from a special-purpose BN, all blocks are cryptographically verified by the BN's verification plugin before they are stored. Block legitimacy is confirmed regardless of the backfill source.

3. Monitor backfill progress by querying `serverStatus` — `lastAvailableBlock` should increase steadily as blocks are fetched and verified:

   ```bash
   grpcurl -plaintext -emit-defaults \
     -import-path ~/bn-proto \
     -proto block-node/api/node_service.proto \
     -d '{}' \
     "$BLOCK_NODE_HOST:40840" \
     org.hiero.block.api.BlockNodeService/serverStatus
   ```

   Full backfill of current network history takes one to several weeks; the BN does not need to complete backfill before the cutover release, but it must be active and making progress.

---

## Verify readiness

After completing all applicable checks, confirm the BN is ready before the maintenance window opens.

|                   Check                   |                                    Command                                    |                           Expected result                            |
|-------------------------------------------|-------------------------------------------------------------------------------|----------------------------------------------------------------------|
| Pod is running                            | `kubectl -n block-node get pods`                                              | `1/1 Running`                                                        |
| Hardware preflight passes                 | `sudo solo-provisioner block node check --profile=mainnet`                    | All steps green                                                      |
| Block store cleared (WRB only)            | `grpcurl ... BlockNodeService/serverStatus`                                   | `firstAvailableBlock = uint64_max`                                   |
| RSA roster loaded (WRB only)              | `grpcurl ... BlockNodeService/serverStatusDetail`                             | Non-empty `rosterHash` in response                                   |
| Greedy backfill enabled (WRB only)        | `helm -n block-node get values <release-name> \| grep BACKFILL_GREEDY`        | `BACKFILL_GREEDY: "true"`                                            |
| Backfill active (WRB only, if configured) | `kubectl -n block-node logs $BN_POD -c block-node-server \| grep -i backfill` | Fetching or completed (if `BACKFILL_BLOCK_NODE_SOURCES_PATH` is set) |
| Alloy shipping                            | `kubectl -n grafana-alloy get pods`                                           | `1/1 Running`                                                        |

If any check fails, resolve the issue and confirm readiness with your Hashgraph PoC before the maintenance window opens.

---

## Troubleshooting

|                                     Symptom                                     |                             Likely cause                             |                                                                                                                     Resolution                                                                                                                     |
|---------------------------------------------------------------------------------|----------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `solo-provisioner block node check` fails with "CPU does not meet requirements" | VM has fewer physical cores than the profile minimum                 | See [Block Node Hardware Specifications](./block-node-hardware-specifications.md). The check counts physical cores, not vCPUs.                                                                                                                     |
| `solo-provisioner block node check` fails with "profile flag is required"       | `--profile` flag missing                                             | Always pass `--profile=mainnet`.                                                                                                                                                                                                                   |
| `block node reset` exits with "permission denied"                               | Command run without `sudo`                                           | Prefix with `sudo`.                                                                                                                                                                                                                                |
| Pod stuck in `0/1` or init containers running after reset                       | Init containers setting up storage and resolving plugins             | Allow 2-5 minutes. Run `kubectl -n block-node describe pod $BN_POD` to see init-container status.                                                                                                                                                  |
| RSA roster missing or not loaded after reset                                    | Mirror Node unreachable, or file missing (file-based delivery)       | If using Mirror Node auto-fetch, confirm `ROSTER_BOOTSTRAP_RSA_MIRROR_NODE_BASE_URL` is set and the Mirror Node is reachable — the roster is re-fetched automatically on pod start. If using file-based delivery, re-deliver from off-host backup. |
| Backfill not starting                                                           | `backfill.blockNodeSourcesPath` is blank or points to a missing file | Confirm `BACKFILL_BLOCK_NODE_SOURCES_PATH` is set and the referenced JSON file exists in the pod.                                                                                                                                                  |
| `firstAvailableBlock` still shows old block numbers after reset                 | Reset did not complete successfully                                  | Check `sudo head /opt/solo/weaver/logs/solo-provisioner.log` for the step that failed.                                                                                                                                                             |
| `grpcurl` returns `connection refused` on port 40840                            | Block Node is not yet listening                                      | Wait for the pod to reach `1/1 Running`; confirm `server.port` in the Block Node configuration.                                                                                                                                                    |

For issues not covered here, see [Block Node Troubleshooting](../troubleshooting.md). When opening a support ticket, attach:

```bash
sudo solo-provisioner -v --output=json
helm -n block-node list
kubectl -n block-node get pods,sts,svc,events
kubectl -n block-node logs $BN_POD -c block-node-server --tail=2000
kubectl -n block-node describe pod $BN_POD
```

---
