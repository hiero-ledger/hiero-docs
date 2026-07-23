# Prerequisites

Complete every item in this page before scheduling the Day Zero installation session. If a
requirement cannot be met, contact Hashgraph DevOps before proceeding.

---

## Coordination prerequisites **[COORDINATED]**

Before scheduling any installation work:

- Operator technical PoCs (primary and backup) are named and reachable
- On-call rotation is defined and includes a 24/7 escalation path pageable by Hashgraph
- A secure secret-exchange channel has been agreed for delivering credentials, TLS material,
  and Alloy tokens
- Operator PoCs are joined to the shared coordination channel with Hashgraph DevOps before
  installation begins - coordination, troubleshooting, and Day Zero incidents all flow through
  this channel

---

## Compute and memory

|      Component      |                                  Requirement                                  |
|---------------------|-------------------------------------------------------------------------------|
| CPU                 | 24 cores / 48 threads, single-socket, ≥ 2.0 GHz base clock                    |
| CPU benchmark floor | Geekbench 6 single-core ≥ 1500; Passmark single-threaded ≥ 2800               |
| RAM                 | 256 GB                                                                        |
| PCIe                | 4.0 or higher (PCIe 3.0 may be bandwidth-limited)                             |
| Sockets             | Single-socket only - dual-socket configurations are not validated (NUMA risk) |

---

## Storage

|             Volume              |      Drive type       |                        Capacity                         |                 Purpose                 |
|---------------------------------|-----------------------|---------------------------------------------------------|-----------------------------------------|
| OS disk (separate, recommended) | SSD, RAID 1 preferred | 240+ GB                                                 | OS only - keep off the NVMe working set |
| Fast NVMe                       | NVMe SSD              | 7.5 TB usable                                           | Recent and live blocks, live state      |
| Bulk HDD                        | HDD                   | 100 TB minimum; 500 TB recommended (≈ 4 yr at 10 k TPS) | Compressed historic block archive       |

> **Enterprise NVMe sizing.** Drives marketed as "8 TB" often ship at 7.84 TB, 7.68 TB, or
> 6.4 TB usable after overprovisioning. Acceptable as long as usable space is ≥ 7.5 TB.
> Usable space may be the aggregate capacity across multiple drives.

Storage performance targets (aggregate across all drives, not per-drive):

|   Tier    | Sustained write | Sustained read | Write IOPS | Read IOPS | Random read AIO | P99 write | P99 read |
|-----------|-----------------|----------------|------------|-----------|-----------------|-----------|----------|
| Fast NVMe | 4 GBps          | 6 GBps         | 350 000    | 900 000   | 1 000 000       | < 300 µs  | < 200 µs |
| Bulk HDD  | 300 MBps        | 1 GBps         | 1 200      | 4 000     | -               | -         | -        |

For full capacity derivations and planning models, see
[Block Node Hardware Specifications](../block-node-hardware-specifications.md).

---

## Network requirements

|               Requirement                |                           Target                           |
|------------------------------------------|------------------------------------------------------------|
| NIC                                      | 2 × 10 Gbps minimum; 25 Gbps or bonded 10 Gbps recommended |
| CN-to-BN latency                         | < 10 ms P95                                                |
| CN-to-BN-to-Client latency               | < 25 ms P95                                                |
| Burst egress at 20 k TPS, 33 subscribers | ≈ 6 Gbps                                                   |
| Public IPv4                              | Static, dedicated to this Block Node host                  |

---

## Firewall and connectivity

The Block Node exposes per-service ports. Lock inbound access to the **publisher port**
(`40984`) to Consensus Node source IPs provided by Hashgraph DevOps. The remaining
per-service ports are accessible to their respective clients. Deny all other inbound by
default.

| Port  |    Service    |                  Restrict to                   |
|-------|---------------|------------------------------------------------|
| 40984 | Publisher     | Consensus Node source IPs (Hashgraph-provided) |
| 40980 | Subscriber    | Mirror Nodes and peer Block Nodes              |
| 40981 | Block Access  | Authorized clients                             |
| 40982 | Server Status | Monitoring and operators                       |
| 40983 | Health        | Monitoring and operators                       |
| 40840 | LoadBalancer  | External clients (MetalLB front-end)           |

```bash
# nftables
# Publisher port — Consensus Nodes only
sudo nft add rule inet filter input ip saddr { <ALLOWED_CN_SOURCE_IPS> } tcp dport 40984 accept
sudo nft add rule inet filter input tcp dport 40984 drop
# Other per-service ports
sudo nft add rule inet filter input tcp dport { 40840, 40980, 40981, 40982, 40983 } accept

# iptables
# Publisher port — Consensus Nodes only
sudo iptables -A INPUT -p tcp -s <ALLOWED_CN_SOURCE_IPS> --dport 40984 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 40984 -j DROP
# Other per-service ports
for port in 40840 40980 40981 40982 40983; do
  sudo iptables -A INPUT -p tcp --dport "$port" -j ACCEPT
done

# ufw
# Publisher port — Consensus Nodes only
sudo ufw allow from <ALLOWED_CN_SOURCE_IPS> to any port 40984 proto tcp
sudo ufw deny 40984/tcp
# Other per-service ports
for port in 40840 40980 40981 40982 40983; do
  sudo ufw allow "$port"/tcp
done
```

Replace `<ALLOWED_CN_SOURCE_IPS>` with the CN public IP(s) provided by Hashgraph DevOps.
Persist rules across reboots per your distribution's conventions.

> **Tip:** If the `/opt/hiero/block-node/application-state/known-publishers.json` configuration
> file is populated with the Consensus Node source IPs, Solo Provisioner handles these firewall
> rules automatically. Two related partner configuration files are also available:
> - `inbound-partners.json` — Block Nodes permitted to backfill from this node.
> - `outbound-partners.json` — Mirror Nodes and partner systems with priority access to the
> `subscribeStream` API.

Outbound requirements:

- Container registry and chart repositories: `ghcr.io`, `raw.githubusercontent.com`,
  `github.com`
- Grafana Alloy remote-write endpoints (TLS outbound) - URLs provided by Hashgraph DevOps
- NTP synchronized
- DNS resolving for `ghcr.io`, the chart repo, and Alloy remotes

Communicate any non-standard port configuration to Hashgraph DevOps before installation.

See [Network Ports and Protocols](../network-ports-and-protocols.md) for the full port reference.

---

## OS and software baseline

- **Operating system:** Ubuntu 24.04 LTS or Debian 13.4 LTS
- `curl` installed; root or `sudo` access required for Solo Provisioner commands
- No pre-existing Kubernetes installation, container runtime, or conflicting `kubelet`

> **Do not pre-install Kubernetes components.** Solo Provisioner installs and manages the full
> stack (kubeadm/kubelet, CRI-O, Cilium, MetalLB, Helm, kubectl, k9s, metrics-server). If any
> of these are already present, plan a clean reimage before proceeding.

---

## TLS decision **[OPERATOR + HASHGRAPH]**

Whether to enable TLS on the Block Node's gRPC endpoint is an operator decision that must be
coordinated with Hashgraph DevOps before installation.

> **Current TLS limitations by port.** As of CN 0.76, TLS on the **publish port (CN → BN)**
> is not supported - the CN PBJ client disables TLS globally, and enabling TLS upstream on this
> port breaks CN streaming. TLS on the **subscriber port** (MN → BN) is permitted. TLS support
> on the publish port is targeted for a future CN release (~0.78/0.79). See
> [Operator FAQ - TLS](../../operator-faq.md#does-the-block-node-support-tls-or-authentication-on-its-endpoints)
> for the full per-port status.

**If enabling TLS on the subscriber port:**

- Configure TLS termination at the firewall, ingress controller, or load balancer — the
  Block Node application does not terminate TLS connections.
- Generate a TLS certificate and private key for `<BLOCK_NODE_FQDN>:40980` - either a
  publicly-trusted certificate or an org-issued certificate from internal PKI
- Deliver the certificate, and full authority chain to Hashgraph DevOps through the agreed
  secure channel
- Record the expiry date and renewal owner - TLS material is operator-owned for the life of
  the Block Node

**If not enabling TLS:**

- Record the decision with Hashgraph DevOps

---

## Hosting and host access

- Tier 1 datacenter posture (physical and logical security, audit-ready) per HIP-1081
- Geographic and provider diversity from other council Block Node operators (Hashgraph tracks)
- Low latency to the operator's Consensus Node where applicable
- Host access (SSH, jump hosts, bastions, MFA, key rotation) is entirely the operator's
  responsibility - Hashgraph does not install or operate access tooling on mainnet operator
  hardware
- On-call engineers must be able to reach the host within the timeframes set out in the
  Operating Agreement

---

## Observability prerequisites **[COORDINATED]**

Receive the following from Hashgraph DevOps through the approved coordination channel before
installation:

- Prometheus remote-write URL (`<PROMETHEUS_REMOTE_WRITE_URL>`)
- Prometheus remote-write username (`<PROMETHEUS_REMOTE_WRITE_USERNAME>`)
- Loki remote-write URL (`<LOKI_REMOTE_WRITE_URL>`)
- Loki remote-write username (`<LOKI_REMOTE_WRITE_USERNAME>`)
- Write-only access tokens for each remote (delivered via secure channel, handled separately
  from the install command)

Telemetry is opt-out for council Tier 1 operators during the initial deployment period.
Contact Hashgraph DevOps if you need to opt out.

---

## Business onboarding prerequisites **[HASHGRAPH]**

The following are confirmed by Hashgraph governance before handoff:

- Technical sponsor and business sponsor assigned
- Node specification approved (CPU/RAM/disk/NIC)
- Hosting facility and geographic location approved (HIP-1081 diversity check)
- Operator `admin_key` structure recorded - BN registration requires signing the registration
  transaction with this key using the Hedera Transaction Tool; Hashgraph provides the signing
  steps at handoff

---

## Network preflight checklist **[OPERATOR]**

Run the following on the host before scheduling the Day Zero session. If any check fails,
resolve it before proceeding - if these fail, the install will too.

```bash
# Resolve container registry and chart repository hosts
for h in ghcr.io raw.githubusercontent.com github.com; do
  getent hosts "$h" >/dev/null && echo "  ok $h" || echo "  FAIL $h"
done

# Confirm outbound TLS reach to Grafana Alloy remotes
curl -sSI --max-time 5 https://<PROMETHEUS_REMOTE_WRITE_URL> | head -1
curl -sSI --max-time 5 https://<LOKI_REMOTE_WRITE_URL> | head -1

# Confirm host's public IP
curl -sS https://ifconfig.me; echo

# Confirm publisher port 40984 reachable from CN source IPs (run from a remote host)
# nc -vz <BLOCK_NODE_PUBLIC_IP> 40984
```

Also confirm:

- Static IPv4 (or IPv6, if preferred and supported by the hosting environment) is actually
  static (not ephemeral/cloud-assigned), externally reachable, and shared with Hashgraph for
  expected-source ACLs
- Inbound ALLOW on TCP 40984 (publisher) from the Hashgraph-provided CN public IP(s) is confirmed
- Any non-standard port configuration communicated to Hashgraph DevOps

---

## Next step

Once all prerequisites above are confirmed, proceed to
[Install the Block Node](./install-block-node.md).
