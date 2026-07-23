# Install the Block Node

> **Before starting:** Complete all items in [Prerequisites](./prerequisites.md)
> and confirm deployment decisions with Hashgraph DevOps.

---

## Step 1 - Confirm deployment decisions **[COORDINATED]**

Before running any install commands, confirm the following with Hashgraph DevOps:

|            Decision             |           Mainnet default           |                          Notes                           |
|---------------------------------|-------------------------------------|----------------------------------------------------------|
| Profile                         | `mainnet` (LFH)                     | Tier 1 is always LFH. Contact Hashgraph if you need RFH  |
| OS                              | Ubuntu 24.04 LTS or Debian 13.4 LTS | Per hardware specification                               |
| Storage sizes                   | Provided by Hashgraph               | Passed as `--*-size` flags at install time               |
| Alloy remote URLs and usernames | Received during Day Minus           | Non-secret; used in the Alloy install command            |
| Cluster name                    | `<BLOCK_NODE_CLUSTER_NAME>`         | Assigned by Hashgraph; format: `lfhNN-mainnet-blocknode` |
| TLS posture                     | Operator decision                   | Must be coordinated before installation                  |
| Deployment values overlay       | `<HASHGRAPH_PROVIDED_VALUES_FILE>`  | Hashgraph ships this file during Day Minus               |

Hashgraph will provide a deployment values file (`<HASHGRAPH_PROVIDED_VALUES_FILE>`) - a thin
overlay on top of the upstream canonical `lfh-values.yaml`. Keep your copy; do not modify it
after handoff without coordination on the shared channel.

---

## Step 2 - Install Solo Provisioner **[OPERATOR]**

```bash
curl -sSL https://raw.githubusercontent.com/hashgraph/solo-weaver/main/install.sh | bash
solo-provisioner --help
```

The `weaver:2500` service account and the `hedera:2000` user (used for storage ownership) are
created automatically during installation. No system users need to be pre-created.

---

## Step 3 - Run preflight checks **[OPERATOR]**

```bash
sudo solo-provisioner block node check --profile=mainnet
```

This validates CPU, memory, disk, dependencies, network connectivity, and storage against the
mainnet profile requirements.

> **If preflight fails, stop.** Resolve all reported issues before proceeding. Do not run the
> install command against a host that fails preflight.

---

## Step 4 - Prepare the RSA bootstrap roster **[COORDINATED]**

The Block Node requires an RSA bootstrap roster at first startup for Wrapped Record Block
(WRB) verification. This is a JSON `AddressBookHistory` which contains repeated
`DatedNodeAddressBook` entries for each historical address book for the network. Each entry
maps Consensus Node IDs to their RSA public keys and other details for a specified range of
blocks.

Default path:
`/opt/hiero/block-node/application-state/rsa-bootstrap-roster.json`
(configurable via `app.state.rsaBootstrapFilePath`)

Hashgraph will confirm the delivery method before installation:

|   Delivery method    |                                                 How it works                                                  |
|----------------------|---------------------------------------------------------------------------------------------------------------|
| Pre-generated file   | Hashgraph ships the file in the chart values; provisioner places it at startup                                |
| Mirror Node fallback | `roster.bootstrap.rsa.mirrorNodeBaseUrl` is configured; the BN fetches and caches the roster on first startup |

> If neither a file nor a Mirror Node URL is configured, the Block Node warns and continues,
> but WRB verification will not work. If the Mirror Node URL is configured but unreachable
> and no local file exists, the Block Node retries indefinitely rather than aborting - it
> surfaces a degraded state and keeps running. If a local cached file exists, the Block Node
> uses it and retries the Mirror Node in the background.

The Block Node also re-queries the mirror node on a regular basis to retrieve any updated
address books. This process will continue until the Cutover release (per HIP-1193), after
which a new BN release will incorporate the final address book history file in the codebase
and the RSA Bootstrap plugin will be removed.

---

## Step 5 - Install the Block Node **[OPERATOR]**

A single command installs the full Kubernetes stack and the Block Node chart:

```bash
sudo solo-provisioner block node install \
  --profile=mainnet \
  --config=/etc/solo-provisioner/config.yaml \
  --values=<HASHGRAPH_PROVIDED_VALUES_FILE> \
  --plugin-preset=tier1-lfh \
  --base-path=/opt/hiero/block-node/data \
  --live-size=2500Gi \
  --archive-size=<ARCHIVE_SIZE> \
  --application-state-size=30Gi \
  --log-size=10Gi
```

**Flag notes:**

- `--config` - Solo Provisioner configuration file (YAML) that sets release-level defaults
  such as namespace, chart version, and storage paths. Hashgraph provides this file
  (`/etc/solo-provisioner/config.yaml`) during Day Minus coordination. Do not modify it after
  handoff without coordination.
- `--values` - Helm values overlay for the Block Node chart, also provided by Hashgraph.
  This is a separate file from `--config`; it configures chart-level settings such as plugins
  and resource limits.
- `--plugin-preset=tier1-lfh` - deploys the Local Full History plugin set required for
  Tier 1 mainnet.

**Storage layout created by this command:**

|       Volume        |   Drive   |            Size             |            Flag            |                       Purpose                       |
|---------------------|-----------|-----------------------------|----------------------------|-----------------------------------------------------|
| `live`              | Fast NVMe | 2.5 TB                      | `--live-size`              | Recent blocks and live state (performance-critical) |
| `archive`           | Bulk HDD  | 80% of provisioned bulk HDD | `--archive-size`           | Compressed historic block archive                   |
| `application-state` | Bulk HDD  | 30 GB                       | `--application-state-size` | Internal application state                          |
| `log`               | OS disk   | 10 GB                       | `--log-size`               | Logs (kept off the NVMe working set)                |

> **Storage sizes are passed as flags, not in the values file.** Solo Provisioner creates the
> PVCs from these flags; the chart references the resulting claims.

**Calculating `<ARCHIVE_SIZE>`:**

Set this to approximately 80% of your provisioned bulk HDD capacity. For example:
- 100 TiB of HDD → `--archive-size=80Ti`
- 500 TiB of HDD → `--archive-size=400Ti`

The 20% headroom allows storage growth to be caught before the volume fills completely. See
[How do I size the archive PVC?](../../operator-faq.md#how-do-i-size-the-archive-pvc-relative-to-my-bulk-storage-disk)
for the full reasoning.

**`<HASHGRAPH_PROVIDED_VALUES_FILE>`** is the cohort-pinned values overlay Hashgraph delivers
during Day Minus. It sets chart defaults appropriate for the current mainnet release.

---

## Step 6 - Verify the install **[OPERATOR]**

Check that the Block Node pod is running:

```bash
kubectl get pods -A
kubectl -n block-node get pods,sts,svc
```

Check the Block Node server status. The grpcurl command requires the Block Node protobuf
bundle, which is published as a release artifact. Download it once and reuse it for all
checks:

```bash
# Download the protobuf bundle for the installed Block Node version
BUNDLE_URL=$(curl -s https://api.github.com/repos/hiero-ledger/hiero-block-node/releases/latest \
  | grep "browser_download_url.*block-node-protobuf.*tgz" \
  | head -1 | cut -d '"' -f 4)
curl -sL -O "$BUNDLE_URL"
tar -xzf block-node-protobuf-*.tgz

# Find the extracted directory name (use it as <VERSION> below)
ls -d block-node-protobuf-*
```

On a fresh install with no blocks ingested yet:

```bash
grpcurl -plaintext -emit-defaults \
  -import-path block-node-protobuf-<VERSION> \
  -proto block-node/api/node_service.proto \
  -d '{}' <BLOCK_NODE_PUBLIC_IP>:40982 \
  org.hiero.block.api.BlockNodeService/serverStatus
```

Replace `<VERSION>` with the directory name from the `ls` output above.

Expected response before the first block arrives:

```json
{
  "firstAvailableBlock": "18446744073709551615",
  "lastAvailableBlock": "18446744073709551615",
  "onlyLatestState": false,
  "versionInformation": null
}
```

The value `18446744073709551615` is `uint64` max - the sentinel for "no blocks received yet."
This value is returned until at least one block from the Consensus Node or backfill is received and stored.

---

## Next step

Proceed to [Configure Alloy Telemetry](./configure-alloy-telemetry.md).

If opting out of telemetry, record the decision with Hashgraph DevOps and proceed directly to
[Network Validation and Go-Live](./network-validation-go-live.md).
