# Steady State Operations

Day-to-day operations reference for Tier 1 Block Node operators on mainnet.

---

## Set the pod variable

Set `$BN` once per session to avoid typing the full pod name in every command:

```bash
export BN=$(kubectl -n block-node get pod \
  -l app.kubernetes.io/name=block-node-server -o name | head -1)
```

---

## Health checks

|              Task               |                                   Command                                    |
|---------------------------------|------------------------------------------------------------------------------|
| Quick health (all namespaces)   | `kubectl get pods -A \| grep -v Running`                                     |
| Block Node pod(s)               | `kubectl -n block-node get pods -l app.kubernetes.io/name=block-node-server` |
| Block Node service and endpoint | `kubectl -n block-node get svc,endpoints`                                    |
| Alloy pod(s)                    | `kubectl -n grafana-alloy get pods`                                          |
| Recent cluster events           | `kubectl get events -A --sort-by=.lastTimestamp \| tail -40`                 |
| k9s TUI                         | `k9s`                                                                        |

---

## Key metrics

Dashboard links are provided by Hashgraph DevOps at handoff. The most important metrics to
watch:

|         Category         |                                                          Metrics                                                          |               What to watch for                |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|------------------------------------------------|
| Node state               | `app_state_status`, `app_historical_oldest_block`, `app_historical_newest_block`                                          | Non-RUNNING status or stalled block height     |
| Ingestion (publisher)    | `publisher_block_items_received`, `publisher_open_connections`, `publisher_stream_errors`, `publisher_receive_latency_ns` | Drop in receive rate, rising latency or errors |
| Verification             | `verification_blocks_failed`, `verification_blocks_error`, `verification_block_time`                                      | Spikes in failures or verification time        |
| Storage                  | `files_recent_total_bytes_stored`, `files_historic_total_bytes_stored`, `files_recent_persistence_time_latency_ns`        | Growth rate or latency spikes                  |
| Messaging / backpressure | `messaging_item_queue_percent_used`, `messaging_notification_queue_percent_used`                                          | Queue saturation                               |
| Subscribers              | `subscriber_open_connections`, `subscriber_errors`                                                                        | Dropped clients or streaming errors            |
| Backfill                 | `backfill_blocks_backfilled`, `backfill_fetch_errors`, `backfill_pending_blocks`                                          | Rising errors or stuck backfill                |
| Cloud archive            | `cloud_storage_archive_failed_tasks`, `cloud_storage_archive_blocks_written`                                              | Archival failures or stalled writes            |
| Host                     | CPU, RAM, disk IO, NIC throughput                                                                                         | Resource saturation                            |

---

## Logs

```bash
kubectl -n block-node logs -f $BN              # tail live
kubectl -n block-node logs $BN --since=5m      # recent logs
kubectl -n block-node logs $BN --previous      # post-crash container logs
kubectl -n block-node exec -it $BN -- bash     # shell into pod
```

Key log tags to grep for:

|             Tag             |               Indicates                |
|-----------------------------|----------------------------------------|
| `StreamPublisherPlugin`     | Incoming blocks from Consensus Nodes   |
| `VerificationServicePlugin` | Signature or proof validation failures |

---

## Pod operations

|                      Task                       |                          Command                           |
|-------------------------------------------------|------------------------------------------------------------|
| Describe pod                                    | `kubectl -n block-node describe $BN`                       |
| Rolling restart                                 | `kubectl -n block-node rollout restart sts/<RELEASE_NAME>` |
| Watch rollout                                   | `kubectl -n block-node rollout status sts/<RELEASE_NAME>`  |
| Solo Provisioner version                        | `solo-provisioner version`                                 |
| Installed Helm releases (find `<RELEASE_NAME>`) | `helm -n block-node list`                                  |

Replace `<RELEASE_NAME>` with the name shown in the `NAME` column of `helm -n block-node list`.

---

## Upgrades **[OPERATOR]**

Upgrade obligations and timing are governed by the Operating Agreement. Hashgraph publishes
chart versions and release notes; operators schedule upgrades against their own
change-management process. Coordinate cohort-wide staggering on the shared channel to avoid
simultaneous restarts. Security-critical upgrades are flagged by Hashgraph DevOps.

```bash
sudo solo-provisioner block node upgrade \
  --profile=mainnet \
  --values=<UPDATED_VALUES_FILE> \
  --no-reuse-values \
  --chart-version=<X.Y.Z>
```

Notes:
- `<UPDATED_VALUES_FILE>` is the values overlay Hashgraph provides for the new release
- `--no-reuse-values` discards any previously applied values and uses only the specified
file - always pass this flag to avoid inheriting stale chart defaults from a previous install
- Use `--with-reset` only when explicitly instructed by Hashgraph DevOps - this clears all
block data and triggers a full re-backfill

---

## Incident reporting

When opening a support ticket, attach:

```bash
solo-provisioner version --output=json
helm -n block-node list
kubectl -n block-node get pods,sts,svc,events
kubectl -n block-node logs $BN --tail=2000
uname -a && uptime && free -h && df -h
```

Escalate through the agreed shared channel with Hashgraph DevOps. For P0 incidents, use the
on-call contact provided at handoff.

---

## Backups and pre-incident hygiene

- Hashgraph does **not** back up `live`, `archive`, or `application-state` volumes - block
  data is reproducible from upstream by design
- Hashgraph retains telemetry shipped via Alloy, subject to platform retention windows
- Keep operator-side copies of `<HASHGRAPH_PROVIDED_VALUES_FILE>`, the RSA bootstrap roster
  file (if using the pre-generated delivery path), and the TLS bundle
- Document the `admin_key` recovery path - losing the key means the registration cannot be
  updated

---

## Related documentation

- [Resetting and Upgrading the Block Node](../resetting-and-upgrading-the-block-node.md)
- [Troubleshooting](../../troubleshooting.md)
- [Operator FAQ](../../operator-faq.md)
- [Metrics Reference](../../metrics.md)
