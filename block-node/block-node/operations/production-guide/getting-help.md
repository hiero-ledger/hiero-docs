# Getting Help

Use the channels below to get support, report issues, and stay current with Block Node
releases and community discussions.

---

## Support channels

Escalate in tier order:

|         Tier         |                                          Channel                                          |                        Use for                        |
|----------------------|-------------------------------------------------------------------------------------------|-------------------------------------------------------|
| 1 - Community        | [Hiero Discord](https://discord.gg/hiero)                                                 | General questions, community discussion               |
| 1 - Issues           | [hiero-block-node GitHub Issues](https://github.com/hiero-ledger/hiero-block-node/issues) | Bug reports, doc improvements                         |
| 2 - Hashgraph DevOps | Shared coordination channel (provided at handoff)                                         | Installation support, upgrade coordination, incidents |
| 3 - P0 escalation    | On-call contact (provided at handoff)                                                     | Production outages requiring immediate response       |

Response timeframes for Tier 2 and 3 are governed by the Operating Agreement,
Exhibit B §7c.

---

## Community

- **Hiero Block Streams Community Group** - `<BLOCK_STREAMS_COMMUNITY_URL>` (obtain from
  Hashgraph DevOps at handoff)
- **Hiero Discord** - [discord.gg/hiero](https://discord.gg/hiero)
- **GitHub Discussions** - [hiero-ledger/hiero-block-node](https://github.com/hiero-ledger/hiero-block-node/discussions)

---

## Reference documentation

### Block Node operator docs

- [Block Node Overview](../../block-node-overview.md)
- [Block Node Hardware Specifications](../block-node-hardware-specifications.md)
- [Network Ports and Protocols](../network-ports-and-protocols.md)
- [Block Node On-Chain Registration (HIP-1137)](../../block-node-on-chain-registration.md)
- [Configuration Reference](../../configuration.md)
- [Metrics Reference](../../metrics.md)
- [Operator FAQ](../../operator-faq.md)
- [Troubleshooting](../../troubleshooting.md)
- [Resetting and Upgrading the Block Node](../resetting-and-upgrading-the-block-node.md)

### Deployment guides

- [Deploy with Solo Provisioner](../solo-weaver-single-node-k8s-deployment.md)
- [Deploy on Bare Metal (Kubernetes)](../single-node-k8s-deployment.md)
- [Solo Provisioner Quickstart](https://github.com/hashgraph/solo-weaver/blob/main/docs/quickstart.md)

### Integration guides

- [Connecting a Mirror Node to a Block Node](../connecting-a-mirror-node-to-a-block-node.md)
- [Configure Consensus Node Streaming](../consensus-node-to-block-node-configuration.md)

### Specifications

- [HIP-1081 - Block Node](https://hips.hedera.com/hip/hip-1081)
- [HIP-1056 - Block Streams](https://hips.hedera.com/hip/hip-1056)
- [HIP-1137 - Block Node Discovery](https://hips.hedera.com/hip/hip-1137)
- [HIP-1357 - Block Node Tier 1 Rewards](https://hips.hedera.com/hip/hip-1357)
- [HIP-1193 - Block Stream Cutover](https://hips.hedera.com/hip/hip-1193)
- [HIP-1200 - hinTS](https://hips.hedera.com/hip/hip-1200)
- [HIP-1424 - Block Merkle Hashing](https://hips.hedera.com/hip/hip-1424)
- [HIP-1427 - Record File Wrapping](https://hips.hedera.com/hip/hip-1427)

---

## Useful diagnostic commands

When opening a support ticket or community thread, include:

```bash
solo-provisioner version --output=json
helm -n block-node list
kubectl -n block-node get pods,sts,svc,events
kubectl -n block-node logs <pod> --tail=500
uname -a && uptime && free -h && df -h
```
