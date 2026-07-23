# Block Node Hardware Specifications

This document defines the minimum hardware requirements and storage/network
benchmark targets for running a Block Node (BN) in a production environment.
It covers [Tier 1](../glossary.md#tier-1-block-node) mainnet, [Tier 2](../glossary.md#tier-2-block-node) rolling-history, and testnet/previewnet
deployments.

---

## Which specification applies to you

|                       Node type                        |        Network        |                                  See section                                  |
|--------------------------------------------------------|-----------------------|-------------------------------------------------------------------------------|
| Tier 1 — receives stream directly from Consensus Nodes | Mainnet               | [Tier 1 Mainnet Server Specifications](#tier-1-mainnet-server-specifications) |
| Tier 2 — receives stream from another Block Node       | Mainnet               | [Tier 2 Server Specifications](#tier-2-server-specifications)                 |
| Any tier                                               | Testnet or Previewnet | [Testnet and Previewnet Sizing](#testnet-and-previewnet-sizing)               |

---

## Tier 1 Mainnet Server Specifications

Two deployment profiles are supported based on how block history is stored at
the Tier 1 level:

### Local Full History (LFH)

All block history is stored locally on the server. The NVMe holds recent/live
blocks and live state; the bulk disk holds the long-term compressed block
archive.

|     Component     |                                                    Minimum Specification                                                    |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------|
| CPU               | 24 cores / 48 threads, single socket, ≥ 2.0 GHz base clock, Geekbench 6 single-core ≥ 1500, Passmark single-threaded ≥ 2800 |
| RAM               | 256 GB                                                                                                                      |
| Fast NVMe Disk    | 7.5 TB NVMe SSD (recent blocks + live state; 7.5TB usable, see note on enterprise sizing and OS disk)                       |
| Bulk Storage Disk | 100 TB HDD or equivalent (compressed block archive)                                                                         |
| Network           | 2 × 10 Gbps NICs                                                                                                            |
| OS                | Linux host OS (Ubuntu 24.04 LTS or Debian 13.x LTS recommended)                                                             |

#### Recommendations

- NICs: 25 Gbps or higher are recommended for better throughput and
  future-proofing, although 10 Gbps is the stated minimum.
- Bulk storage: 500 TB is recommended for [LFH](../glossary.md#local-full-history-lfh) to accommodate long-term block
  history and state growth. A lower 300 TB is considered adequate, potentially
  with a shorter upgrade timeline, and 100 TB is the minimum requirement for
  Tier 1.
- Servers may be sourced from bare metal providers or cloud providers offering
  dedicated instances. LFH configurations require significant storage capacity
  and are typically sourced from bare metal providers or purchased outright for
  self hosting or colocation.
- **Enterprise NVMe sizing**: Enterprise-grade drives marketed as "8 TB" are
  commonly shipped at lower usable capacities (e.g. 7.84 TB, 7.68 TB, or
  6.4 TB) due to overprovisioning for endurance. These capacities are
  acceptable provided the application's usable space requirement is met: 7.5 TB.
- **OS disk**: A **separate dedicated drive for the OS is strongly recommended** so
  that the OS does not compete with the application for NVMe space and disk I/O. This
  is not always possible due to port and drive slot limitations on some server models, but it should be
  prioritized when possible.
  If no separate OS drive is available and the OS must share the Fast NVMe:
  - A minimum of **at least the application's working set** of NVMe space must
    remain available to the block node at all times; do not allow the OS
    partition to grow unbounded, and allow at least 10 GB for OCI image storage.
  - Logs and other ephemeral OS data should be **eagerly reclaimed** (e.g. via
    aggressive log rotation) to avoid crowding out application I/O.
  - Scheduled maintenance tasks (log rotation, `tmpwatch`, `journald` vacuum,
    etc.) should **not** be configured to run at or near UTC midnight, when
    block-node I/O activity is typically elevated.

---

## Tier 2 Server Specifications

[Tier 2 Block Nodes](https://github.com/hiero-ledger/hiero-block-node/blob/main/docs/Block-Node-Types.md)
receive their block stream from another Block Node rather than directly from
Consensus Nodes. Most Tier 2 nodes are expected to operate as Rolling-History
nodes, retaining only a configurable window of recent blocks. Tier 2 nodes are
still required to verify the block stream they receive, but are not required to
store the full history of the network.

### Full History Tier 2

Tier 2 nodes that choose to store the complete block history and manage live state should use the
[Tier 1 LFH specification](#local-full-history-lfh).

### Remote Full History (RFH)

Block history is stored remotely (e.g. cloud object store).
Historical data is offloaded to object storage n real time after processing.
No state services are offered and most if not all services are expected to be private.

|     Component     |                                                    Minimum Specification                                                    |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------|
| CPU               | 16 cores / 32 threads, single socket, ≥ 2.0 GHz base clock, Geekbench 6 single-core ≥ 1500, Passmark single-threaded ≥ 2800 |
| RAM               | 128 GB                                                                                                                      |
| Bulk Storage Disk | 100 GB HDD or equivalent                                                                                                    |
| Network           | 2 × 10 Gbps NICs                                                                                                            |
| OS                | Linux host OS (Ubuntu 24.04 LTS or Debian 13.x LTS recommended)                                                             |

### Rolling-History (Partial History) Tier 2

A Rolling-History Tier 2 node does not store full blockchain history. CPU and
RAM requirements match Tier 1 — the primary driver is the State Management task
(which handles live state and serves downstream subscribers), not verification
or persistence alone. Exact minimums for nodes that do not manage live state
have not yet been formally benchmarked. The `stream-publisher` plugin is not
deployed, so there is no inbound stream from Consensus Nodes. Bulk HDD storage
is sized to the retention window rather than the full block history.

|     Component     |                               Minimum Specification                               |
|-------------------|-----------------------------------------------------------------------------------|
| CPU               | 24 cores / 48 threads, single socket, ≥ 2.0 GHz base clock                        |
| RAM               | 256 GB                                                                            |
| Fast NVMe Disk    | 7.5 TB NVMe SSD (recent blocks + live state; partially optional — see note below) |
| Bulk Storage Disk | Size to retention window (see table below)                                        |
| Network           | 10 Gbps NIC minimum (see [Network Requirements](#network-requirements))           |
| OS                | Linux host OS (Ubuntu 24.04 LTS or Debian 13.x LTS recommended)                   |

> **Note:** The Fast NVMe Disk is at least partially optional for Rolling-History Tier 2 nodes. Its
> necessity depends on whether the node manages live state and serves downstream subscribers. Consult
> your Hashgraph PoC for the current recommended configuration if you are not managing live state.

**Bulk storage by retention window (at 10K TPS mainnet, 20% headroom):**

| Retention | On-disk estimate (zstd, 20% headroom) |
|-----------|--------------------------------------:|
| 7 days    |                                2.8 TB |
| 30 days   |                               11.9 TB |
| 90 days   |                               35.6 TB |
| 1 year    |                              144.5 TB |

Estimates use the same block-size model as the mainnet tables below:
`Block_zstd = 88,963 + 372.8 × T bytes` (T = transactions per block at 10K TPS
mainnet). The 7.5 TB NVMe tier supports approximately 19 days of blocks at 10K
TPS with 20% headroom. Operators retaining longer windows should provision
additional HDD bulk storage.

---

## Testnet and Previewnet Sizing

Testnet and previewnet run at significantly lower TPS than mainnet, reducing
both CPU load and storage requirements.

### Recommended VM sizing

For automated deployment using [Solo Provisioner](https://github.com/hashgraph/solo-weaver),
select a machine with at least 16 vCPUs and 32 GB RAM (for example,
GCP `e2-standard-16`) for `previewnet` or `testnet` profiles. For local testing
and learning deployments, `e2-standard-8` (4 physical cores, 8 vCPUs, 32 GB
RAM) is the minimum. See the
[Virtual Machine Single Node Kubernetes Deployment Guide](./solo-weaver-single-node-k8s-deployment.md)
for the full step-by-step walkthrough.

> **Note:** [Solo Provisioner](../glossary.md#solo-provisioner) (`sudo solo-provisioner block node install -p testnet`
> or `-p previewnet`) handles storage provisioning automatically based on the
> selected profile. Manual sizing from this section is needed only when
> deploying outside the Solo Provisioner flow.

### Storage estimates

Applying the block size model at lower TPS:

| TPS | On-disk / block (zstd) | Per day (zstd) | Per month (zstd) |
|----:|-----------------------:|---------------:|-----------------:|
| 100 |                0.13 MB |          11 GB |           330 GB |
| 500 |                0.28 MB |          24 GB |           715 GB |

Actual testnet block sizes vary with transaction mix; these figures serve as
planning estimates. Storage requirements are significantly smaller than mainnet.

---

## Storage Benchmark Targets

The Block Node is I/O-intensive. The following benchmarks define the
**aggregate** sustained throughput, IOPS, and latency targets that storage must
meet to avoid becoming a bottleneck. Values represent aggregate disk performance
across all drives in the configuration, not per-drive requirements.

### Disk Performance Targets

| Disk Type | Sustained Write | Sustained Read |  Write IOPS   |   Read IOPS   | Random Read AIO IOPS | P99 Write Latency | P99 Read Latency |
|-----------|-----------------|----------------|---------------|---------------|----------------------|-------------------|------------------|
| Fast NVMe | 4 GBps          | 6 GBps         | 350k (random) | 900k (random) | 1M                   | < 300 µs          | < 200 µs         |
| Bulk Disk | 300 MBps        | 1 GBps         | 1200          | 4000          | n/a                  | —                 | —                |

#### Notes

- IOPS profile numbers are averages; peak IOPS will be defined by the speed of
  cache, not the speed of the disk itself.
- The Fast NVMe disk serves recent/live block storage and live state management;
  the Bulk Disk serves the historic block archive in LFH configurations.
- P99 latency targets apply only to the Fast NVMe tier. Bulk Disk latency is
  workload-dependent and not explicitly bounded.

#### Bulk tier hardware

- **Medium**: HDD is the intended medium for the bulk tier. SSD or NVMe is not
  required at the 100 TB+ capacity and is typically cost-prohibitive at that
  scale.
- **Aggregate, not per-drive**: The IOPS and bandwidth targets are aggregate
  across all drives in the configuration, not per-drive requirements.
  Achievable with at least 12 drives in RAID-0; fewer may be sufficient
  depending on the specific hardware.
- **Caching layer**: With sufficient physical drives, a dedicated read/write
  cache layer in front of the bulk drives is not needed and is not
  recommended.

---

## Network Requirements

|       Requirement        |            Target             |
|--------------------------|-------------------------------|
| Minimum NIC throughput   | 10 Gbps (25 Gbps recommended) |
| CN ↔ BN latency          | < 10ms total P95              |
| CN ↔ BN ↔ Client latency | < 25ms total P95              |

#### Notes

- Consensus Nodes (CNs) and Block Nodes (BNs) must have strong and stable
  network connections without excessive latency.
- Excessive (over 30ms) inter-node latency risks stream backpressure and
  increased buffering requirements.

---

## Network Throughput and Storage Growth Estimates

This section provides capacity planning estimates for operators sizing storage
and network links. All figures are derived from a linear block-size model fitted
to 13 real mainnet blocks from an ~11K TPS mixed-workload test (R² = 0.9996).
The model constants are:

```
Block_zstd(T) =   88,963 + 372.8 × T   bytes   (on-disk, zstd-compressed)
Block_raw(T)  =  245,737 + 910.5 × T   bytes   (wire, uncompressed)

T = transactions per block = TPS × block_interval
```

#### Assumptions used in the tables below

- Block interval: 1 second (1 block/sec — conservative; mainnet in early 2026
  runs at 0.5 blocks/sec)
- Compression ratio: 2.39× (zstd, from v3 mixed-workload model)
- Worst-case egress subscribers: 33 (13 Block Nodes backfilling +
  10 Mirror Nodes + 10 DApps)
- Worst-case ingress: 4 parallel catch-up streams from Consensus Nodes
  (workload assumption for capacity planning, not a software-enforced limit;
  the actual per-node TCP connection cap is configured by
  `server.maxTcpConnections`, default 1000)

### Block Size by TPS

Derived directly from the model constants above.

|    TPS | Tx/block | On-disk / block (zstd) | Wire size / block (raw) |
|-------:|---------:|-----------------------:|------------------------:|
|  2,000 |    2,000 |                0.83 MB |                 1.58 MB |
| 10,000 |   10,000 |                3.82 MB |                 8.86 MB |
| 20,000 |   20,000 |                7.54 MB |                17.96 MB |

### Daily and Monthly On-Disk Storage (local block files, zstd)

> These figures cover raw block storage only. Allow additional headroom for Live
> State, indexes, overhead, and recent working files.

|    TPS | Per day (zstd) | Per month (zstd) |
|-------:|---------------:|-----------------:|
|  2,000 |          72 GB |           2.2 TB |
| 10,000 |         330 GB |           9.9 TB |
| 20,000 |         652 GB |          19.6 TB |

#### Planning target (20% headroom over model)

|    TPS | Per day (planned) | Per month (planned) |
|-------:|------------------:|--------------------:|
|  2,000 |             86 GB |              2.6 TB |
| 10,000 |            396 GB |             11.9 TB |
| 20,000 |            782 GB |             23.5 TB |

### Ingress Bandwidth (Consensus Node → Block Node)

Steady-state ingress carries one uncompressed block stream.
Worst-case reflects 4 Consensus Nodes simultaneously streaming to a single BN
(flow-control limited).

|    TPS | Steady-state ingress | Worst-case ingress (4× catch-up) |
|-------:|---------------------:|---------------------------------:|
|  2,000 |            ~1.8 MB/s |                          ~8 MB/s |
| 20,000 |           ~17.5 MB/s |                         ~70 MB/s |

> NIC sizing is driven by egress (see below), not ingress.
>
> Ingress values are all based assuming uncompressed data, with HTTP automatic
> compression a reasonable compression value of 3x smaller may be used.

### Egress Bandwidth (Block Node → Subscribers)

Each downstream subscriber (Mirror Node, Block Node, DApp) receives its own
uncompressed stream. All figures use the raw (uncompressed) wire size.

|    TPS | Per subscriber / day | Per subscriber / month | 33 subscribers / day | 33 subscribers / month |
|-------:|---------------------:|-----------------------:|---------------------:|-----------------------:|
|  2,000 |               156 GB |                 4.6 TB |               5.1 TB |                 154 TB |
| 20,000 |               1.5 TB |                  45 TB |                50 TB |                 1.5 PB |

**Worst-case peak bandwidth (burst — 4 in-flight blocks per subscriber):**

|    TPS | Steady-state egress (33 sub) | Burst egress (33 sub, 4× in-flight) |
|-------:|-----------------------------:|------------------------------------:|
|  2,000 |                     ~60 MB/s |                            ~67 MB/s |
| 20,000 |                    ~580 MB/s |                           ~648 MB/s |

> At 20K TPS with 33 active subscribers, burst egress approaches ~6 Gbps
> Block Nodes serving many live subscribers at high TPS **require** at least a
> 10 Gbps NIC and may need 25 Gbps or multiple bonded 10 Gbps links for headroom.
>
> Egress values are all based assuming uncompressed data; with HTTP automatic
> compression a reasonable compression value of 3x smaller may be used.

### Sizing Summary

| Scenario | On-disk (1 year, zstd, no headroom) | Peak ingress | Burst egress (33 sub) | NIC minimum |
|----------|------------------------------------:|-------------:|----------------------:|------------:|
| 2K TPS   |                             26.3 TB |      80 Mbps |             ~600 Mbps |     10 Gbps |
| 20K TPS  |                              237 TB |     700 Mbps |               ~6 Gbps |    10+ Gbps |

> The 100 TB bulk disk minimum (LFH) covers approximately 4 years at 2K TPS or
> approximately 5 months at 20K TPS.
> The recommended 500 TB covers approximately 4 years at 10K TPS.

---

## Additional Considerations

- **Clock speed**: Base CPU clock speed must be ≥ 2.0 GHz. Higher clock speeds
  reduce per-block processing latency, which is important for keeping up with
  mainnet block production rates.
- **CPU socket configuration**: Only single-socket configurations have been
  tested. Dual-socket configurations are not recommended until explicitly
  validated; operators using dual-socket hardware do so at their own risk and
  should expect potential NUMA-related performance issues.
- **PCIe generation**: PCIe 4.0 or higher is required to sustain the combined
  NVMe and network maximum throughput targets above. PCIe 3.0 configurations may
  be bandwidth-limited.
- **OS disk**: A separate dedicated OS drive is strongly recommended. If the OS
  shares the Fast NVMe disk, ensure sufficient capacity remains reserved for the
  application, reclaim ephemeral data aggressively, and avoid scheduling
  maintenance tasks at UTC midnight. Allow at least 10 GB for OCI image storage.
