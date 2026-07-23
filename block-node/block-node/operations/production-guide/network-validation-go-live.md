# Network Validation and Go-Live

> **Before starting:** The Block Node must be running and Alloy must be confirmed shipping
> (or telemetry opt-out recorded) before starting this page.

---

## Network validation **[HASHGRAPH]**

Hashgraph runs an end-to-end validation against a Hashgraph-operated reference endpoint
during the Day Zero session. No operator-side preparation is required beyond the prerequisites.

This step confirms:
- The Block Node is reachable on its public endpoints
- Block ingest is working correctly from a test Consensus Node
- Metrics and logs are landing in the configured remotes (if telemetry is enabled)

After validation, the Block Node is reset to a clean state before it enters production.

---

## Reset before go-live **[OPERATOR]**

After network validation, reset the Block Node. This clears all state accumulated during
testing so the production Block Node starts clean:

```bash
sudo solo-provisioner block node reset --profile=mainnet
```

This command:
1. Scales the StatefulSet to 0
2. Clears all data directories (`live`, `archive`, `application-state`, `log`)
3. Scales the StatefulSet back to 1

> **This reset is mandatory.** Without it, test data accumulated during validation pollutes
> the production Block Node. Do not skip this step.

Confirm the RSA bootstrap roster is in place at the expected path before the Block Node
restarts. See [Install the Block Node - Step 4](./install-block-node.md#step-4--prepare-the-rsa-bootstrap-roster-coordinated).

---

## Network inclusion **[COORDINATED]**

Network inclusion requires signing the Block Node registration transaction with the `admin_key`
using the Hedera Transaction Tool. Hashgraph DevOps coordinates this step and provides the
signing procedure at handoff.

On the Consensus Node side, inclusion is config-driven via `block-nodes.json`. Hashgraph
manages this configuration and the node-management tooling that distributes it to the
Consensus Nodes.

> **`admin_key` custody.** If the `admin_key` is lost, the Block Node registration cannot be
> updated. Treat it as a production signing key from day one and document the recovery path
> before go-live.

For details on the on-chain registration process, see
[Block Node On-Chain Registration](../../block-node-on-chain-registration.md).

---

## Handoff and close **[COORDINATED]**

Hashgraph DevOps confirms:
- The Block Node is ingesting blocks from the configured Consensus Node
- The Block Node is reachable on its public endpoints
- Hashgraph adds the Block Node `/healthz` health endpoint to the central health monitor and
standard dashboards

The operator is formally added to the upgrade pool after this step.

---

## Backfill expectations

After reset, the Block Node backfills block history from upstream Block Nodes. This is not
instantaneous.

Full backfill of current history - approximately 20 TB and growing - may take **days to
several weeks**, depending on:
- Network throughput to upstream Block Nodes
- Bulk disk write performance on this host
- Total history size at the time of deployment

Hashgraph will confirm the backfill approach before handoff:

|        Approach         |                                                      How it works                                                      |
|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| **Live backfill**       | The Block Node comes up after reset and fills history over a multi-week window                                         |
| **Pre-loaded snapshot** | The operator restores an archive snapshot to the bulk volume after reset, then the Block Node catches up only the tail |

Track backfill progress:
- `serverStatus` - the `firstAvailableBlock` / `lastAvailableBlock` range widens as backfill proceeds
- Metrics: `backfill_pending_blocks`, `backfill_blocks_backfilled`, `backfill_fetch_errors`

---

## Next step

Proceed to [Steady State Operations](./steady-state-operations.md).
