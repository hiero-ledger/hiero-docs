# Configure the Cloud Storage Expanded Plugin

The `cloud-storage-expanded` plugin archives every verified block to an S3-compatible object
store, giving you long-term block storage independent of the Block Node's local disk. Typical
uses include off-cluster backup, compliance archiving, and making historical blocks available to
tooling that reads directly from S3. Each block is stored as a single ZSTD-compressed Protobuf
object, independently retrievable by block number.

The plugin supports AWS S3, Google Cloud Storage (via S3 interoperability), and any other
S3-compatible store. Uploads run asynchronously in the background and do not slow block
ingestion. However, if uploads fail persistently beyond the configured retry window, the plugin
reports a terminal persistence failure, which causes the publisher plugin to end all active
handlers. Size the retry window (`cloud.storage.expanded.retryMaxAgeSeconds`) to tolerate
transient S3 outages without triggering a publisher disconnect.

---

## Prerequisites

- A deployed Block Node. See
  [Deploy with Solo Provisioner](./solo-weaver-single-node-k8s-deployment.md) or
  [Manual Kubernetes Deployment](./single-node-k8s-deployment.md).
- `helm` v3 and `kubectl` installed and configured to access the namespace where your Block Node
  is deployed.
- An S3-compatible bucket with write access. Create one using your cloud provider's console or
  CLI (AWS S3, Google Cloud Storage, or any S3-compatible service) before proceeding.
- S3 credentials for the bucket: an access key ID and secret access key. The plugin's underlying
  S3 client has no credential chain and no IAM role or Workload Identity fallback; these two
  values are the only supported authentication method. The required S3 permission is `s3:PutObject`
  on the target bucket. See your cloud provider's documentation for how to create static credentials:
  [AWS IAM access keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) ·
  [GCS HMAC keys](https://cloud.google.com/storage/docs/authentication/hmackeys).
  Then see [Configure credentials](#configure-credentials) for how to supply them to the plugin.
- `aws` CLI or `gcloud` CLI (for the verification step that confirms objects appear in the
  bucket). Install only the CLI matching your cloud provider.

---

## Enable the plugin

The `cloud-storage-expanded` plugin is not loaded by default. Append it to the end of your
existing `plugins.names` list in your Helm override file - do not replace the full list, just
add the new entry. If you do not have your current active values, retrieve them first:

```bash
helm get values <release-name> -n block-node --all | grep "names:"
```

Then append `cloud-storage-expanded` at the end:

```yaml
plugins:
  names: "<your-existing-plugins>,cloud-storage-expanded"
```

Apply the change (replace `block-node` with your actual Helm release name). For the OCI
registry path, pin `--version` to your currently installed chart version to avoid an
unintended upgrade - run `helm list -n block-node` to find it:

```bash
# Installed from the OCI registry:
helm upgrade block-node oci://ghcr.io/hiero-ledger/hiero-block-node/block-node-server \
  --version <chart-version> \
  -n block-node \
  -f your-values.yaml

# Running from the repository:
helm upgrade block-node ./charts/block-node-server \
  -n block-node \
  -f your-values.yaml
```

The chart resolves and downloads the plugin JAR from the configured Maven repositories on the
next pod start. No image rebuild is required. Watch the pod restart and wait until it is ready
(typically 1–3 minutes while the JAR downloads):

```bash
kubectl get pods -n block-node -l app.kubernetes.io/name=block-node-server -w
# Press Ctrl+C when the pod shows 1/1 Running
```

> **Note:** Removing `cloud-storage-expanded` from `plugins.names` also removes its JAR. When the
> plugin list changes, the chart's `resolve-plugins` init container deletes the JARs it resolved
> before and resolves the new list on the next pod start.

---

## Configure credentials

Plugin properties can be set as environment variables (`CLOUD_STORAGE_EXPANDED_*` naming
convention) or as JVM system properties (`-Dcloud.storage.expanded.*` via `JAVA_TOOL_OPTIONS`).
JVM system properties take priority when both are set. For the full environment variable
reference see
[Cloud Storage Expanded Plugin Configuration](../configuration.md#cloud-storage-expanded-plugin-configuration).

Add the required endpoint and bucket settings to `blockNode.config` in your Helm override
file, in the same file where you set `plugins.names`:

```yaml
blockNode:
  config:
    CLOUD_STORAGE_EXPANDED_ENDPOINT_URL: "https://s3.amazonaws.com/"
    CLOUD_STORAGE_EXPANDED_BUCKET_NAME: "my-block-archive"
    CLOUD_STORAGE_EXPANDED_REGION_NAME: "us-east-1"
```

> **Note:** `blockNode.config` entries are stored in a Kubernetes ConfigMap (unencrypted).
> Never put credentials in `blockNode.config` - use `blockNode.secretRef` instead.

Set `CLOUD_STORAGE_EXPANDED_ACCESS_KEY` and `CLOUD_STORAGE_EXPANDED_SECRET_KEY` via a
Kubernetes Secret. The chart injects all Secret keys as pod environment variables, which
Swirlds Config maps to the corresponding plugin properties.

Create the secret:

```bash
kubectl create secret generic s3-credentials \
  -n block-node \
  --from-literal=CLOUD_STORAGE_EXPANDED_ACCESS_KEY=your-access-key \
  --from-literal=CLOUD_STORAGE_EXPANDED_SECRET_KEY=your-secret-key
```

Reference it in your Helm override file. `blockNode.secretRef` takes a single Secret: if you
already reference one, add the two keys to that Secret instead. `secretRef` is ignored when
`blockNode.secret` is set.

```yaml
blockNode:
  secretRef: s3-credentials
```

---

## Core configuration

All properties below can be set as environment variables (`CLOUD_STORAGE_EXPANDED_*`) or as
`-D` JVM system properties in `JAVA_TOOL_OPTIONS` (higher priority when both are set). See
[Configure credentials](#configure-credentials) for examples.

|                   Property                    |  Default   | Required |                                                 Description                                                  |
|-----------------------------------------------|:----------:|:--------:|--------------------------------------------------------------------------------------------------------------|
| `cloud.storage.expanded.endpointUrl`          | _(blank)_  |   Yes    | S3-compatible endpoint URL (e.g. `https://s3.amazonaws.com/`). Blank disables the plugin with a WARNING.     |
| `cloud.storage.expanded.bucketName`           | _(blank)_  |   Yes    | Name of the S3 bucket to upload blocks into. Blank disables the plugin with a WARNING.                       |
| `cloud.storage.expanded.regionName`           | _(blank)_  |   Yes    | AWS or S3-compatible region name (e.g. `us-east-1`). Blank disables the plugin with a WARNING.               |
| `cloud.storage.expanded.objectKeyPrefix`      | _(blank)_  |    No    | Prefix prepended to every object key (e.g. `blocks`). Leave blank for no prefix.                             |
| `cloud.storage.expanded.storageClass`         | `STANDARD` |    No    | S3 storage class. Only `STANDARD` is accepted; use bucket lifecycle policies for archive tiering.            |
| `cloud.storage.expanded.accessKey`            | _(blank)_  |   Yes    | S3 access key (not logged). Required; must not be blank. No IAM role or credential-chain fallback.           |
| `cloud.storage.expanded.secretKey`            | _(blank)_  |   Yes    | S3 secret key (not logged). Required; must not be blank. No IAM role or credential-chain fallback.           |
| `cloud.storage.expanded.uploadTimeoutSeconds` |    `60`    |    No    | Seconds to wait for in-flight uploads during Block Node shutdown before treating them as failed. Minimum: 1. |

> **Note:** All five required properties (`endpointUrl`, `bucketName`, `regionName`, `accessKey`,
> `secretKey`) must not be blank. Each blank property is named in a WARNING at startup, and the
> plugin skips all uploads for the life of the process. Correct the value and restart the Block Node to recover.

---

## Configure the retry buffer

When an upload fails due to a transient S3 or network error, the plugin buffers the already-
compressed block bytes in memory and retries in the background. The buffer is purely in memory —
no data is written to disk - so any buffered blocks are lost if the process restarts before
recovery succeeds. A deferred `succeeded=false` notification is sent to downstream plugins only
once retries are exhausted, preventing unnecessary reconnection storms on transient failures.

|                    Property                    | Default |                                                                                                 Description                                                                                                 |
|------------------------------------------------|:-------:|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `cloud.storage.expanded.retryEnabled`          | `true`  | Enable background retry. When `false`, a failed upload is reported as a terminal failure immediately with no buffering.                                                                                     |
| `cloud.storage.expanded.retryIntervalSeconds`  |  `10`   | Period of the background retry tick and the per-block backoff after a failed attempt. Each tick re-attempts only blocks whose backoff has elapsed. Minimum: 1.                                              |
| `cloud.storage.expanded.retryMaxAgeSeconds`    |  `60`   | Maximum time in seconds a block may remain buffered before it is dropped and reported as a terminal failure. Minimum: 1.                                                                                    |
| `cloud.storage.expanded.retryMaxPendingBlocks` |  `30`   | Maximum number of blocks held in the in-memory retry buffer at once. At capacity, the new block is still buffered and the longest-buffered block is evicted and reported as a terminal failure. Minimum: 1. |

The defaults bound memory usage tightly: at most 30 blocks are buffered, each for at most
60 seconds. Increase `retryMaxAgeSeconds` or `retryMaxPendingBlocks` only if your S3 endpoint
experiences outages longer than one minute or high block rates exhaust the 30-block cap before
the retry interval fires.

---

## Object key format

Each block is stored using a 4 / 4 / 4 / 4 / 3 zero-padded folder hierarchy derived from the
19-digit block number. The hierarchy provides lexicographic ordering and efficient S3
prefix-based partitioning.

```
{objectKeyPrefix}/AAAA/BBBB/CCCC/DDDD/EEE.blk.zstd
```

| Block number |                Object key                 |
|:------------:|-------------------------------------------|
|      1       | `blocks/0000/0000/0000/0000/001.blk.zstd` |
|  1 234 567   | `blocks/0000/0000/0000/1234/567.blk.zstd` |
| 108 273 182  | `blocks/0000/0000/0010/8273/182.blk.zstd` |

If `objectKeyPrefix` is blank, the hierarchy path is stored directly at the bucket root with no
leading `/`.

---

## Verify the configuration

After the pod restarts, confirm the plugin is active and uploading. First get your pod name —
you will need it for the steps below:

```bash
kubectl get pods -n block-node -l app.kubernetes.io/name=block-node-server
```

Example output:

```
NAME                               READY   STATUS    RESTARTS   AGE
block-node-block-node-server-0     1/1     Running   0          2m
```

1. Check the Block Node startup logs for WARNING messages. If any of the following appear, the
   plugin is inactive - correct the named property and restart the pod:

   ```bash
   kubectl logs -n block-node <pod-name> | grep -E "cloud.storage.expanded|Failed to initialize S3 client"
   ```

   If a required property is blank, output contains one or more of:

   ```
   cloud.storage.expanded.accessKey is blank; S3 uploads will be skipped until configured.
   cloud.storage.expanded.bucketName is blank; S3 uploads will be skipped until configured.
   cloud.storage.expanded.endpointUrl is blank; S3 uploads will be skipped until configured.
   cloud.storage.expanded.regionName is blank; S3 uploads will be skipped until configured.
   cloud.storage.expanded.secretKey is blank; S3 uploads will be skipped until configured.
   ```

   A healthy start logs `S3 client initialized successfully`. Other matches are expected: the
   startup configuration dump lists every `cloud.storage.expanded.*` property, and each plugin log
   line includes its package name. No output at all means the plugin is not loaded; check
   `plugins.names`.

2. Query the Prometheus metrics endpoint (default port `16007`) to confirm the upload counter
   is incrementing. Forward the port if the pod is not directly reachable:

   ```bash
   kubectl port-forward -n block-node <pod-name> 16007:16007
   ```

   Then in a second terminal:

   ```bash
   curl -s http://localhost:16007/metrics | grep cloud_expanded_total_uploads
   ```

   Expected output:

   ```
   # HELP blocknode_cloud_expanded_total_uploads_total Number of blocks successfully uploaded to S3-compatible storage
   # TYPE blocknode_cloud_expanded_total_uploads_total counter
   blocknode_cloud_expanded_total_uploads_total 0.0
   ```

   The counter starts at `0.0` and increments with each archived block. Allow 30–60 seconds
   after startup for the first blocks to be verified and uploaded before expecting a non-zero
   value. Re-run the command after waiting to confirm it is rising.

3. Confirm objects appear in your bucket. Allow 1–2 minutes after startup for the first objects
   to appear:

   ```bash
   # AWS S3:
   aws s3 ls s3://my-block-archive/blocks/ --recursive | head -10

   # Google Cloud Storage:
   gcloud storage ls "gs://my-block-archive/blocks/**" | head -10
   ```

   A healthy listing shows `.blk.zstd` files under your configured prefix:

   ```
   2024-01-15 10:23:45      4096 blocks/0000/0000/0000/0000/001.blk.zstd
   2024-01-15 10:23:46      4112 blocks/0000/0000/0000/0000/002.blk.zstd
   ```

---

## Monitor uploads

The plugin exposes the following metrics under the `blocknode_` prefix. Prometheus appends
`_total` to counter names - for example, `cloud_expanded_total_uploads` becomes
`blocknode_cloud_expanded_total_uploads_total`. Use the full Prometheus names in PromQL queries
and alert rules. See [Metrics](../metrics.md#cloud-expanded) for alerting recommendations.

|                 Metric                 |                                           Description                                            |
|----------------------------------------|--------------------------------------------------------------------------------------------------|
| `cloud_expanded_total_uploads`         | Blocks successfully uploaded (first attempt or after retry recovery).                            |
| `cloud_expanded_total_upload_failures` | Blocks that ended in terminal failure (compression error, retry disabled, or retries exhausted). |
| `cloud_expanded_total_upload_bytes`    | Compressed bytes successfully transferred.                                                       |
| `cloud_expanded_upload_latency_ns`     | Total wall-clock time spent in upload calls, in nanoseconds.                                     |
| `cloud_expanded_pending_retry_blocks`  | Current number of blocks buffered in memory awaiting a background retry upload.                  |
| `cloud_expanded_retry_success`         | Blocks recovered by background retry after an initial upload failure.                            |
| `cloud_expanded_retry_exhausted`       | Blocks dropped after exhausting retries, evicted from a full retry buffer, or left at shutdown.  |

A non-zero `blocknode_cloud_expanded_pending_retry_blocks` means uploads are failing and being
retried. Any increase in `blocknode_cloud_expanded_total_upload_failures_total` is a terminal
failure that was reported downstream as not persisted. Use the
following commands to diagnose:

```bash
# Verify AWS credentials and identity:
aws sts get-caller-identity

# Test S3 endpoint reachability:
curl -I https://s3.amazonaws.com/

# Check bucket policy:
aws s3api get-bucket-policy --bucket <your-bucket-name>
```

For GCS:

```bash
# Test authentication:
gcloud auth application-default print-access-token

# Test endpoint reachability:
curl -I https://storage.googleapis.com/
```
