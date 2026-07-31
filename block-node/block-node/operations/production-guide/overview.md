# Production Node Operation Guide - Overview

> **Scope:** Tier 1 Block Node operators deploying to a production network (mainnet, testnet,
> or future Hiero networks) using the Local Full History (LFH) profile. Examples use
> `--profile=mainnet`; substitute the appropriate profile for your target network. Tier 2
> deployment is not covered here.

---

## Who this guide is for

This guide is written for:

- **Council operators** deploying a Tier 1 mainnet Block Node
- **Sysadmins and DevOps engineers** who own the host, Kubernetes cluster, and lifecycle
- **Technical stakeholders** who need to understand the end-to-end deployment process

Readers are assumed to be comfortable with Linux system administration, Kubernetes basics,
and Helm. This is a production deployment guide, not a beginner walkthrough.

---

## What this guide covers

A Tier 1 Block Node runs as a Helm chart inside a single-node Kubernetes cluster on
operator-owned bare-metal hardware. The supported interface for installation and lifecycle
management is **Solo Provisioner** (`solo-provisioner`), distributed from the
[`solo-weaver`](https://github.com/hashgraph/solo-weaver) repository.

|                 Component                 |                                                                                     Role                                                                                     |
|-------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Block Node** (`hiero-block-node`)       | Ingests block streams from Consensus Nodes, maintains live state, serves block data to subscribers                                                                           |
| **Solo Provisioner** (`solo-provisioner`) | Installs and manages the full host stack: Kubernetes (kubeadm/kubelet, CRI-O, Cilium, MetalLB), the Block Node Helm chart via a Kubernetes Operator (CRD), and Grafana Alloy |
| **Grafana Alloy**                         | Telemetry agent shipping Prometheus metrics and Loki logs to the operator's observability infrastructure                                                                     |

This guide covers:

|                               Page                                |                         Content                         |
|-------------------------------------------------------------------|---------------------------------------------------------|
| [Prerequisites](./prerequisites.md)                               | Hardware, network, TLS, and pre-deployment coordination |
| [Install the Block Node](./install-block-node.md)                 | Solo Provisioner install with production storage flags  |
| [Configure Alloy Telemetry](./configure-alloy-telemetry.md)       | Grafana Alloy setup and remote-write configuration      |
| [Network Validation and Go-Live](./network-validation-go-live.md) | Validation, reset, network inclusion, and backfill      |
| [Steady State Operations](./steady-state-operations.md)           | Health checks, upgrades, and incident reporting         |
| [Disaster Recovery](./disaster-recovery.md)                       | Four failure scenarios and resolution steps             |
| [Getting Help](./getting-help.md)                                 | Support channels, community, and reference links        |

---

## Responsibility model

Steps across this guide are labeled with who performs them:

- **[OPERATOR]** - the council operator runs this independently
- **[HASHGRAPH]** - performed or confirmed by Hashgraph DevOps before proceeding
- **[COORDINATED]** - both parties need to be present or in communication

---

## Sensitive values

Some values in this guide are intentionally not published. Placeholders indicate
operator-specific or Hashgraph-provided inputs. Obtain Hashgraph-provided values through
your approved coordination channel. Never commit secrets or credentials to version control.

Common placeholders used in this guide:

|                   Placeholder                   |            Who provides it            |
|-------------------------------------------------|---------------------------------------|
| `<BLOCK_NODE_PUBLIC_IP>`                        | Operator                              |
| `<BLOCK_NODE_FQDN>`                             | Operator                              |
| `<BLOCK_NODE_CLUSTER_NAME>`                     | Hashgraph DevOps                      |
| `<ALLOWED_CN_SOURCE_IPS>`                       | Hashgraph DevOps                      |
| `<HASHGRAPH_PROVIDED_VALUES_FILE>`              | Hashgraph DevOps (via secure channel) |
| `<PROMETHEUS_REMOTE_WRITE_URL>` and `_USERNAME` | Hashgraph DevOps                      |
| `<LOKI_REMOTE_WRITE_URL>` and `_USERNAME`       | Hashgraph DevOps                      |
| `<PROMETHEUS_REMOTE_WRITE_PASSWORD>`            | Hashgraph DevOps (via secure channel) |
| `<LOKI_REMOTE_WRITE_PASSWORD>`                  | Hashgraph DevOps (via secure channel) |

---

## Related documentation

- [Block Node Hardware Specifications](../block-node-hardware-specifications.md)
- [Network Ports and Protocols](../network-ports-and-protocols.md)
- [Block Node On-Chain Registration](../../block-node-on-chain-registration.md)
- [Operator FAQ](../../faq/operator-faq.md)
- [Solo Provisioner Quickstart](https://github.com/hashgraph/solo-weaver/blob/main/docs/quickstart.md)
