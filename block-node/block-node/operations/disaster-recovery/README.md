# Block Node Disaster Recovery Playbooks

Operational runbooks for recovering from Block Node failure scenarios. Each playbook covers a single scenario: how to confirm you are in it, how to recover, and how to verify recovery is complete.

**Use these playbooks when something has already gone wrong.** For planned maintenance, upgrades, and non-emergency procedures, see the [Block Node documentation](../../README.md).

**These playbooks live on GitHub only and are not published to GitBook.** They contain operational commands, internal escalation paths, and environment-specific placeholders not suitable for public documentation.

---

## Find your playbook

Match what you are seeing to a playbook. If you are not sure, open the closest match and let the **"Is this your doc?"** section at the top confirm or redirect you.

|                            If you are seeing this...                             |                                          Go to                                           |
|----------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
| All transactions are fully halted, multiple CNs show backpressure                | PB-04 *(draft - [#3786](https://github.com/hiero-ledger/hiero-block-node/issues/3786))*  |
| One or two Block Nodes are down but the network is still processing transactions | PB-02 *(coming - [#3784](https://github.com/hiero-ledger/hiero-block-node/issues/3784))* |
| Mirror Node has stopped receiving new blocks or is significantly behind          | PB-03 *(coming - [#3785](https://github.com/hiero-ledger/hiero-block-node/issues/3785))* |
| One or more CNs cannot publish blocks but the network is running                 | PB-05 *(coming - [#3787](https://github.com/hiero-ledger/hiero-block-node/issues/3787))* |
| Block Node is returning verification errors or block proofs are failing          | PB-06 *(coming - [#3788](https://github.com/hiero-ledger/hiero-block-node/issues/3788))* |
| TSS / hinTS signature ceremony is failing or roster change is stuck              | PB-07 *(coming - [#3789](https://github.com/hiero-ledger/hiero-block-node/issues/3789))* |
| You need to restore a Block Node from a snapshot (procedure, not reactive)       | PB-01 *(coming - [#3783](https://github.com/hiero-ledger/hiero-block-node/issues/3783))* |

---

## Full index

|  ID   |                        Scenario                         | Severity  |   Primary executor    |                                     Status                                      |
|-------|---------------------------------------------------------|-----------|-----------------------|---------------------------------------------------------------------------------|
| PB-01 | Block Node bootstrap from snapshot                      | Procedure | DevOps / Operator     | Planned ([#3783](https://github.com/hiero-ledger/hiero-block-node/issues/3783)) |
| PB-02 | Single or partial Tier-1 Block Node loss                | SEV-2     | Operator + DevOps     | Planned ([#3784](https://github.com/hiero-ledger/hiero-block-node/issues/3784)) |
| PB-03 | Mirror Node falls behind or loses BN subscription       | SEV-3     | Mirror Node team      | Planned ([#3785](https://github.com/hiero-ledger/hiero-block-node/issues/3785)) |
| PB-04 | Network-wide backpressure due to BN streaming failure   | SEV-1     | Hashgraph DevOps      | Draft ([#3786](https://github.com/hiero-ledger/hiero-block-node/issues/3786))   |
| PB-05 | Individual CN(s) unable to publish blocks               | SEV-2     | Council Node Operator | Planned ([#3787](https://github.com/hiero-ledger/hiero-block-node/issues/3787)) |
| PB-06 | Block Node data corruption or block verification errors | SEV-1     | DevOps + Engineering  | Planned ([#3788](https://github.com/hiero-ledger/hiero-block-node/issues/3788)) |
| PB-07 | hinTS / TSS signature failure                           | SEV-1     | Hashgraph Engineering | Planned ([#3789](https://github.com/hiero-ledger/hiero-block-node/issues/3789)) |

**Severity:** SEV-1 = transactions halted or data integrity at risk · SEV-2 = degraded redundancy, no transaction halt · SEV-3 = downstream consumers affected, consensus unaffected

---

## Adding a new playbook

1. Copy [`_template.md`](./_template.md) and name the new file `NN-short-scenario-name.md`.
2. Fill in every section. Do not leave placeholder text in a committed file.
3. Add a row to the "Find your playbook" table and the full index in this README.
4. Open a PR - these playbooks do not go through the GitBook `SUMMARY.md` flow.

---

[Block Node Documentation](../../README.md)
