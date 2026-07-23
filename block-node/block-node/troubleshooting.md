# Block Node Troubleshooting

## Overview

This page is a troubleshooting runbook for
[**Hiero Block Nodes**](https://github.com/hiero-ledger/hiero-block-node).
It assumes you are an operator with SSH or kubectl access to both the node and
the appropriate Prometheus / Grafana UI.

**Use it when:**

- Block ingest or [backfill](./glossary.md#backfill) stalls
- Subscriber or Mirror Node cannot connect
- Disk or storage is under pressure
- Metrics or dashboards look wrong

---

### Observability & Diagnostics

Block Nodes are generally robust, but like any distributed system component,
operators occasionally encounter issues related to networking, storage,
synchronization, or configuration. The reference implementation includes
comprehensive logging, CLI tools, and Prometheus / Grafana metrics to identify
and resolve problems quickly.

#### 1.1 Logs & diagnostics

- **Logs**: Single-line text logs are written to stdout / stderr
  (easily ingested by Loki, ELK, Splunk, etc.). In Docker / Kubernetes the
  format is `java.util.logging.SimpleFormatter`; local dev uses a coloured
  single-line variant (`CleanColorfulFormatter`).
- **Log levels**: Controlled via
  [values.yaml](https://github.com/hiero-ledger/hiero-block-node/blob/main/charts/block-node-server/values.yaml)
  (`blockNode.logs.level`): `ALL FINEST FINER FINE CONFIG INFO WARNING SEVERE OFF`.
- **Key log tags to grep**:
  - `StreamPublisherPlugin` : incoming blocks from consensus nodes
  - `VerificationServicePlugin` : signature / proof failures

##### Example log lines

```text
block-node-server 2026-02-03 21:53:01.831+0000 INFO [org.hiero.block.node.backfill.BackfillFetcher getNewAvailableRange] Unable to reach node [BackfillSourceConfig[address=rfh01.previewnet...]], skipping
block-node-server 2026-02-03 21:53:01.831+0000 FINER [org.hiero.block.node.backfill.BackfillPlugin detectAndScheduleGaps] Nothing to backfill: startBound=[500] endCap=[1]
block-node-server 2026-02-03 21:53:03.748+0000 FINER [org.hiero.block.node.health.HealthServicePlugin handleLivez] Responded code 200 (OK) to liveness check
```

#### 1.2 Prometheus metrics & monitoring

The Block Node exposes a rich set of Prometheus metrics on `/metrics`
(default port 16007). Key Grafana dashboards are available in the official
[Hiero Block Node `dashboards/` folder.](https://github.com/hiero-ledger/hiero-block-node/tree/main/charts/block-node-server/dashboards)

All metrics are prefixed with `blocknode`
(for example, `blocknode_publisher_block_items_received`).
See the full list in the
[metrics reference](https://github.com/hiero-ledger/hiero-block-node/blob/main/docs/block-node/metrics.md#metrics-by-plugin).

|       **Category**        |                                                   **Important Metrics**                                                   |                 **What to Watch For**                  |
|---------------------------|---------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------|
| Node State                | `app_state_status`, `app_historical_oldest_block`, `app_historical_newest_block`                                          | Non‑RUNNING status or stalled block height progress    |
| Ingestion (Publisher)     | `publisher_block_items_received`, `publisher_open_connections`, `publisher_stream_errors`, `publisher_receive_latency_ns` | Drop in receive rate, rising latency, or stream errors |
| Verification              | `verification_blocks_failed`, `verification_blocks_error`, `verification_block_time`, `hashing_block_time`                | Spikes in failures or verification time                |
| Storage (Recent/Historic) | `files_recent_total_bytes_stored`, `files_historic_total_bytes_stored`, `files_recent_persistence_time_latency_ns`        | Storage growth or persistence latency spikes           |
| Messaging / Backpressure  | `messaging_item_queue_percent_used`, `messaging_notification_queue_percent_used`                                          | Queue saturation / backpressure                        |
| Subscribers               | `subscriber_open_connections`, `subscriber_errors`                                                                        | Dropped clients or streaming errors                    |
| Backfill                  | `backfill_blocks_backfilled`, `backfill_fetch_errors`, `backfill_pending_blocks`, `backfill_status`                       | Rising errors or stuck backfill status                 |
| Cloud Archive             | `cloud_storage_archive_failed_tasks`, `cloud_storage_archive_blocks_written`                                              | Archival failures or stalled block write count         |

---

Use the runbooks below during incidents. Each follows a consistent pattern:

- **Triage** – confirm what is actually broken
- **Logs / Metrics / Configuration** – narrow down root cause
- **Resolution** – apply fixes
- **Verification** – confirm recovery

### Block Node not receiving new blocks

> **Tip:** Use this runbook when ingest appears stalled and logs show little or no publish activity for new blocks.

1. **Triage**
   - Confirm symptoms:
     - `publisher_block_items_received` flat or near-zero.
     - `publisher_open_connections` dropping toward zero.
     - Logs show no recent block publish events.
   - Check node health:
     - Verify process is running and not crashlooping.
     - Confirm CPU / memory are not obviously saturated.
   - From a trusted host, test connectivity to the Block Node:
     - `nc -vz <IP_OF_BLOCK_NODE> 40840`
       - Success: TCP reachability is OK.
       - Failure: suspect firewall, security group, or local iptables.
   - If `nc` fails:
     - Verify host firewall rules on both sides (Block Node and CN).
     - Check any intermediate firewalls / load balancers for drops.
     - Confirm the correct IP and port for the CN endpoint.
2. **Logs**
   - Search for:
     - Connection-related errors to consensus nodes.
     - Repeated reconnect attempts or backoff warnings.
3. **Metrics**
   - Confirm:
     - `publisher_block_items_received` has stalled.
     - `publisher_stream_errors` is non-zero.
   - Correlate with time of any infrastructure changes (deploys, config updates, firewall changes).
4. **Resolution**
   - Fix firewall / security group rules on gossip / ingest port (default `40840`, or configured port).
   - Restart the Block Node if needed once connectivity is restored.
5. **Verification**
   - Confirm `publisher_block_items_received` increases steadily.
   - `publisher_open_connections` is stable and non-zero.
   - Logs show continuous block publish / ingest activity.

---

### Block Node operator: subscribers cannot connect

> If you operate a Mirror Node that cannot subscribe to a Block Node, see [Mirror Node operator: Mirror Node cannot connect to Block Node](#mirror-node-operator-mirror-node-cannot-connect-to-block-node) instead.

1. **Triage**
   - Confirm symptoms from client side:
     - gRPC connection failures, timeouts, or TLS errors when subscribing.
     - Clients repeatedly reconnecting or backing off.
   - Confirm on the Block Node:
     - Service is running and listening on the expected gRPC port.
     - No obvious CPU / memory starvation.
2. **Network and endpoint checks**
   - From a trusted host (for example, Mirror Node):
     - `nc -vz <IP_OF_BLOCK_NODE> <GRPC_PORT>`
       - Success: TCP reachability is OK.
       - Failure: suspect firewall, security group, or local iptables.
     - Optional gRPC sanity checks (requires `grpcurl` and proto files, which can be found in the [Hiero Block Node repo release artifacts](https://github.com/hiero-ledger/hiero-block-node/releases)):

       ```sh
       grpcurl -plaintext -proto /path/to/block_access_service.proto \
           <IP_OF_BLOCK_NODE>:<GRPC_PORT> \
           org.hiero.block.api.BlockAccessService/getBlock \
           '{"retrieve_latest": true}'

       grpcurl -plaintext -proto /path/to/block_stream_subscribe_service.proto \
           <IP_OF_BLOCK_NODE>:<GRPC_PORT> \
           org.hiero.block.api.BlockStreamSubscribeService/subscribeBlockStream \
           '{"start_block_number": <BLOCK>, "end_block_number": <BLOCK>}'
       ```
   - Verify the correct advertised hostname / IP and port in Block Node config, and that DNS or load balancer points to the active node.
3. **TLS (if TLS is enabled at your ingress or proxy)**
   - Check client logs for:
     - `x509: certificate has expired or is not yet valid`.
     - Hostname mismatch between certificate and endpoint.
     - Unknown CA / trust failures.
   - TLS termination is at the ingress or reverse proxy in front of the Block Node, not on the Block Node itself. Confirm TLS cert and key paths are correct, readable, and not expired at that layer.
4. **Service configuration**
   - Ensure `stream-subscriber` is present in the Block Node plugin configuration.
   - Check any rate limits or `max-connections` settings that might be rejecting clients.
5. **Resolution**
   - Fix endpoint configuration (advertise address / port), update DNS or load balancer if needed.
   - If using a TLS-terminating proxy or ingress, renew or reinstall certificates there and restart the proxy as needed.
   - Update firewall / security groups to allow gRPC traffic from subscribers.
6. **Verification**
   - Confirm clients successfully establish long-lived gRPC streams without continuous reconnects.
   - `blocknode_subscriber_open_connections` is stable and non-zero; `blocknode_subscriber_errors` is not climbing.

---

### Mirror Node operator: Mirror Node cannot connect to Block Node

> **Tip:** Use this runbook when the Mirror Node's last committed block is not advancing, importer logs show repeated subscribe errors, or all configured Block Nodes are reported as inactive.

1. **Triage**
   - Confirm symptoms:
     - Mirror Node importer logs show repeated subscribe failures or all Block Nodes marked inactive.
     - The Mirror Node's last committed block is not advancing.
     - `blocknode_subscriber_open_connections` on the Block Node side is zero or not incrementing (if you have access to it).
2. **Network reachability**
   - From the Mirror Node host, confirm TCP connectivity to each configured Block Node:

     ```bash
     nc -vz <BLOCK_NODE_HOST> 40840
     ```

     - **Success**: `Connection to <BLOCK_NODE_HOST> port 40840 succeeded!` — proceed to the next step.
     - **Failure**: TCP reachability is broken. Check firewall rules, security groups, and that the Block Node process is running. Confirm the port matches `hiero.mirror.importer.block.nodes[].port` in the Mirror Node configuration.
3. **Block Node status**
   - Query `serverStatus` to confirm blocks are available and the gRPC endpoint is responding.
     See [Step 1 in Connecting a Mirror Node to a Block Node](./operations/connecting-a-mirror-node-to-a-block-node.md#step-1-confirm-each-block-node-is-reachable-and-serving-blocks) for the exact command.
     - If both `firstAvailableBlock` and `lastAvailableBlock` equal `18446744073709551615`, the Block Node has not yet ingested any blocks — wait before subscribing.
     - If `serverStatus` itself fails to connect, the Block Node may be unreachable, the port may be wrong, or TLS is required but not configured on the client side.
4. **Subscribe smoke test**
   - Attempt a manual `subscribeBlockStream` call from the Mirror Node host to confirm the Block Node will accept a subscription and identify the exact terminal status code.
     See [Step 3 in Connecting a Mirror Node to a Block Node](./operations/connecting-a-mirror-node-to-a-block-node.md#step-3-smoke-test-the-subscribe-call-from-the-shell-optional) for the exact command.
   - Match the returned `status` code against the [Troubleshooting table in Connecting a Mirror Node to a Block Node](./operations/connecting-a-mirror-node-to-a-block-node.md#troubleshooting) to identify the specific cause and resolution.
5. **Mirror Node configuration**
   - Verify:
     - `hiero.mirror.importer.block.enabled` is `true`.
     - `hiero.mirror.importer.block.nodes[].host` and `.port` match the actual Block Node endpoint.
     - `hiero.mirror.importer.block.nodes[].requiresTls` matches whether the endpoint uses TLS (`false` for plain gRPC, `true` if TLS is terminated at the Block Node or a load balancer in front of it).
     - `hiero.mirror.importer.block.sourceType` is `BLOCK_NODE` (or `AUTO` to try Block Node first and fall back to block files from cloud storage if the Block Node becomes unavailable).
6. **Resolution**
   - After correcting any configuration, restart the Mirror Node importer and re-run the reachability and smoke-test checks above to confirm the fix.
   - For each specific terminal status code or error message, consult the [Troubleshooting table in Connecting a Mirror Node to a Block Node](./operations/connecting-a-mirror-node-to-a-block-node.md#troubleshooting).
7. **Verification**
   - Confirm the Mirror Node's last committed block advances monotonically.
   - Importer logs show active subscribe sessions without repeated reconnects.
   - If you operate the Block Node, `blocknode_subscriber_open_connections` is non-zero and `blocknode_subscriber_errors` is not climbing.
8. **Escalation**
   - If the steps above do not resolve the issue, collect the following before opening a ticket:
     - Output of `nc -vz <BLOCK_NODE_HOST> <PORT>`.
     - Output of the `serverStatus` call (or the error it returned).
     - Mirror Node importer log excerpt showing subscribe errors (remove sensitive values).
     - Mirror Node `application.yml` block configuration section (remove credentials).
     - Block Node version, if available.
   - If the Block Node returns unexpected status codes, errors, or is unreachable despite correct networking, open an issue in [`hiero-ledger/hiero-block-node`](https://github.com/hiero-ledger/hiero-block-node/issues).
   - If the Mirror Node configuration appears correct but the importer still does not connect or process blocks as expected, open an issue in [`hiero-ledger/hiero-mirror-node`](https://github.com/hiero-ledger/hiero-mirror-node/issues).

---

### Disk full / out of space

1. **Triage**
   - Confirm symptoms:
     - Node crashes, refuses new blocks, or logs I/O errors.
     - Alerts on storage utilization from Grafana / Prometheus.
   - Check capacity on the host:
     - `df -h` for filesystem usage.
     - `iostat`, `files_recent_total_bytes_stored`, and `files_recent_persistence_time_latency_ns` for pressure.
2. **Identify what is consuming space**
   - Determine which volume(s) hold block data, snapshots, and logs.
   - Inspect directories for unexpected growth (logs, temp, or backup folders).
   - *TODO: document the canonical data directory layout for Block Nodes (paths for blocks, snapshots, logs).*  ← GAP
3. **Short-term mitigation**
   - If safe, rotate / compress / prune logs.
   - If using partial-history nodes, enable or adjust pruning according to policy.
   - Temporarily add storage space to the affected volume if possible.
4. **Longer-term resolution**
   - For archival needs, migrate to a node with larger storage or externalize cold data.
   - Tune retention settings for blocks, snapshots, and logs.
   - Ensure monitoring alerts fire well before 100% usage (for example, at 75%, 85%, 95%).
5. **Verification**
   - Confirm `files_recent_total_bytes_stored` (and/or `files_historic_total_bytes_stored`) and host `df -h` fall below alert thresholds.
   - Block ingest resumes normally and no further I/O errors appear in logs.

---

### Metrics endpoint not accessible

1. **Triage**
   - Confirm that Grafana / Prometheus cannot scrape `/metrics` for this Block Node.
   - Attempt to curl from a nearby host:
     - `curl -v http://<IP_OF_BLOCK_NODE>:16007/metrics`
       - Success: HTTP 200 with Prometheus text output.
       - Failure: connection refused / timeout.
2. **Node-local checks**
   - From the node itself:
     - `curl -v http://localhost:16007/metrics`
       - If this fails, suspect local config or process issues.
   - Confirm `metrics.exporter.openmetrics.http.port` matches the expected port (default `16007`).
     In Kubernetes, check `blockNode.metrics.port` in your Helm values.
3. **Bind address**
   - If localhost works but remote scrape fails, verify the metrics server binds to
     `0.0.0.0` (all interfaces) and not `127.0.0.1` (localhost only).
   - Check `blockNode.metrics.hostname` in your Helm values (default: `0.0.0.0`).
   - Outside Kubernetes, check `metrics.exporter.openmetrics.http.hostname` in
     `app.properties` or via `-Dmetrics.exporter.openmetrics.http.hostname=0.0.0.0`.
   - Binding to `127.0.0.1` will cause all remote Prometheus scrapes to fail with
     "connection refused".
4. **Network and firewall**
   - If the bind address is correct but remote scrape still fails:
     - Check host firewall / security group rules for port `16007`.
     - Confirm any load balancers or service meshes expose the metrics port.
5. **Scraper configuration**
   - Verify Prometheus target configuration:
     - Correct job name, scheme (http / https), and port.
     - No incorrect path overrides.
6. **Resolution**
   - Enable metrics in config and restart the Block Node if required.
   - Open or adjust firewall / security rules for the metrics port.
   - Fix Prometheus / Grafana scrape configuration.
7. **Verification**
   - Confirm the Prometheus target is `UP` and `up\{job="block-node-metrics", instance="\<node\>"\} == 1`.
   - Grafana dashboards populate and scrape errors clear.

---

### Blocks are not being backfilled

1. **Triage**
   - Confirm symptoms:
     - Gaps in block height or missing historical ranges expected to be present.
     - Backfill-related alerts firing.
   - Check logs for explicit backfill warnings or errors.
2. **Metrics and topology**
   - Review backfill metrics such as `blocknode_backfill\*`:
     - Backfill rate, errors, and lag.
   - Confirm which upstream nodes this node is allowed to backfill from and that they are healthy.
3. **Configuration checks**
   - Verify `BLOCK_NODE_EARLIEST_MANAGED_BLOCK` reflects the earliest block this node should own.
   - Verify `BACKFILL_START_BLOCK` / `BACKFILL_END_BLOCK` are set correctly relative to available upstream history.
   - Ensure backfill is enabled in the node configuration and not paused.
4. **Network and permissions**
   - Confirm this node can reach upstream Block Nodes over the required ports.
   - Check that any authentication / TLS between nodes is valid.
5. **Resolution**
   - Correct misconfigured earliest-block values (`BLOCK_NODE_EARLIEST_MANAGED_BLOCK`, `BACKFILL_START_BLOCK`, `BACKFILL_END_BLOCK`) and apply changes.
   - Restart the Block Node if configuration changes require it.
   - If upstream history is incomplete, coordinate with operators of archival nodes to provide the missing range.
6. **Verification**
   - Monitor backfill metrics until the node catches up to the desired block height.
   - Confirm local block height matches expected network height for the configured range.

---

### Quick Reference Table

The table below is a **summary-only quick reference**. Use the runbooks above for full diagnosis and remediation steps.

|                    **Issue**                     |                               **Symptoms**                                |                                              **Diagnosis**                                               |                                                                  **Resolution**                                                                   |
|--------------------------------------------------|---------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| Node not receiving new blocks                    | Ingest appears stalled, logs show no publish activity                     | Check firewall on ingest port (default `40840`), Consensus Node logs                                     | Open inbound port, ensure node is authorized / whitelisted by upstream CN; check ingress cert if using TLS termination                            |
| Block Node operator: subscribers cannot connect  | gRPC connection failures; clients repeatedly reconnecting                 | Endpoint config; `stream-subscriber` plugin present; TLS cert check at ingress (if TLS is enabled)       | Fix endpoint or firewall; renew ingress TLS certs (if TLS is enabled); add `stream-subscriber` to plugin configuration                            |
| Mirror Node operator: Mirror Node cannot connect | MN block height not advancing; repeated subscribe errors in importer logs | `nc` reachability; `serverStatus` output; subscribe smoke test; MN `block.*` config                      | Fix endpoint or firewall; correct `requiresTls`; see [connecting guide](./operations/connecting-a-mirror-node-to-a-block-node.md#troubleshooting) |
| Disk full / out of space                         | Node crashes or refuses new blocks                                        | `df -h`, `files_recent_total_bytes_stored` nearing limit                                                 | Prune old blocks (partial-history), expand volume, or migrate to archive node                                                                     |
| Metrics endpoint not accessible                  | Grafana dashboards empty, Prometheus target `DOWN`                        | Port `16007` blocked or metrics disabled via config                                                      | Open port, enable metrics, fix Prometheus scrape job                                                                                              |
| Blocks not being backfilled                      | Log entries show backfill warnings, missing historical ranges             | Check `blocknode_backfill*` metrics, `BLOCK_NODE_EARLIEST_MANAGED_BLOCK` / `BACKFILL_START_BLOCK` config | Fix earliest-block config, restart node if required, ensure healthy upstream archival source                                                      |

---

For help, open a GitHub issue in [`hiero-ledger/hiero-block-node`](https://github.com/hiero-ledger/hiero-block-node/issues).
