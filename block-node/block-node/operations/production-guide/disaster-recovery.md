# Disaster Recovery

When something goes wrong, open a support ticket first (see
[Incident reporting](./steady-state-operations.md#incident-reporting)), then use the matching
scenario below.

> **Reset blast radius.** `sudo solo-provisioner block node reset --profile=mainnet` destroys
> all block data on this node. The Block Node re-syncs from upstream (multi-week backfill). Only
> reset when the scenario calls for it or when explicitly instructed by Hashgraph DevOps.

---

## Scenario A - Block Node pod unhealthy, host fine

**Symptoms:** crash-looping, pod not reaching Ready state, or producing errors on an otherwise
healthy host.

1. Capture pod logs, events, and status before taking action:

   ```bash
   kubectl -n block-node describe <pod>
   kubectl -n block-node logs <pod> --previous
   kubectl get events -n block-node --sort-by=.lastTimestamp | tail -20
   ```
2. Attempt a rolling restart:

   ```bash
   kubectl -n block-node rollout restart sts/<RELEASE_NAME>
   kubectl -n block-node rollout status sts/<RELEASE_NAME>
   ```
3. If still failing, escalate to Hashgraph DevOps with the captured diagnostics.
4. Last resort - reset the Block Node (destroys all block data; full re-backfill required):

   ```bash
   sudo solo-provisioner block node reset --profile=mainnet
   ```

---

## Scenario B - Data corruption or inconsistent state

**Symptoms:** verification errors, mismatched block hashes, stuck queues.

1. **Pause before resetting.** Corruption is rare and the diagnostics are valuable - capture
   logs and metrics before taking any action.
2. Engage Hashgraph DevOps via the shared coordination channel.
3. Once Hashgraph DevOps approves a reset:

   ```bash
   sudo solo-provisioner block node reset --profile=mainnet
   ```
4. Confirm the RSA bootstrap roster is in place at the expected path before the Block Node
   restarts.
5. Re-backfill begins automatically. Monitor progress per
   [Backfill expectations](./network-validation-go-live.md#backfill-expectations).

---

## Scenario C - Host loss (hardware failure or full reimage)

1. New hardware must meet the Tier 1 LFH specification —
   see [Prerequisites](./prerequisites.md#compute-and-memory).
2. Re-run the full deployment workflow:
   - [Install the Block Node](./install-block-node.md)
   - [Configure Alloy Telemetry](./configure-alloy-telemetry.md) (if telemetry is enabled)
3. Re-run network validation with Hashgraph DevOps per
   [Network Validation and Go-Live](./network-validation-go-live.md).
4. Reset and re-backfill.
5. No re-registration is required as long as the `admin_key` is preserved. Coordinate any
   public IP or FQDN changes with Hashgraph DevOps before go-live.

---

## Scenario D - Region or facility outage

A single-node Tier 1 operator has no in-region failover by design. Other Tier 1 Block Nodes
continue ingesting during one operator's outage - this is the redundancy model per HIP-1081.

1. Notify Hashgraph DevOps via the shared coordination channel as soon as the outage is known.
2. Provide an estimated return-to-service time.
3. On recovery, resume from Scenario A or C as appropriate.

> Active/passive failover and per-operator multi-region deployment are not part of the Tier 1
> reference profile. Raise as a separate design discussion if your organization requires that
> posture.

---

## Upgrade with reset

When a major schema migration requires clearing storage, Hashgraph DevOps will explicitly
instruct you to use `--with-reset`:

```bash
sudo solo-provisioner block node upgrade \
  --profile=mainnet \
  --values=<UPDATED_VALUES_FILE> \
  --no-reuse-values \
  --chart-version=<X.Y.Z> \
  --with-reset
```

Do not use `--with-reset` unless explicitly instructed. This is rare.

---

## Related documentation

- [Troubleshooting](../../troubleshooting.md)
- [Resetting and Upgrading the Block Node](../resetting-and-upgrading-the-block-node.md)
- [Operator FAQ](../../operator-faq.md)
