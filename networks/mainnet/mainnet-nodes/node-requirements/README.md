---
description: >-
  The Hedera Mainnet is currently comprised of permissioned consensus nodes
  operated by the Hedera Governing Council
---

# Node Requirements

The following is provided to help [_Hedera Governing Council_](https://hedera.com/council) members deploy their permissioned mainnet consensus node. Please note, this information is not intended to apply to Hedera's transition to a permissionless network.

## Minimum Node Platform Requirements

Currently, the Hedera Mainnet will perform at a rate determined by the lowest-performing node. To ensure a common level of performance minimum hardware, connectivity, and hosting requirements have been defined for the initial permissioned, Governing Council nodes.

{% hint style="warning" %}
To ensure accurate conformity with the minimum requirements, please provide node hardware, connectivity, and hosting details to Hedera prior to purchase (devops@swirldslabs.com).
{% endhint %}

* CPU: X86/X64 compatible (Intel Xeon or AMD EPYC); 24 cores/48 threads meeting or exceeding the following benchmarks:
  * Geekbench 6 single-core score
    * Minimum: 1000 or greater
    * Recommended: 1500 or greater
  * Passmark single thread rating:
    * Minimum to remain on Mainnet: 2300 or greater
    * Recommended: 2800 or greater
* Network Connectivity: Sustained 1Gb/s internet bandwidth via a single 1-Gigabit / 10-Gigabit Ethernet interface
* Memory: 256 GB PC4-21300 2666MHz DDR4 ECC Registered DIMM or faster (minimum), 320GB or higher PC4-25600 3200MHz (recommended)
* Storage: It is recommended to mount 240 GB SSD with Raid 1 as a root volume `/` and then provide usable storage via different devices later mounted during installation. This may not be possible on your hardware, so alternatively all required storage may be allocated to the root volume.
  * Minimum: 5TB of SSD NVMe usable storage
  * Recommended:
    * 2 x 240GB SSD with RAID 1 for OS Storage
    * 2 x NVMe devices as a 7.5TB RAID 0 (or 4x as RAID 10 array)
* Storage performance: If mounted to root volume, the root volume must meet these requirements. If provisioned via RAID, the RAID array should meet these requirements:
  * Sequential write sustained:
    * Minimum: 2,000GB/s
    * Recommended: 3,000GB/s
  * Sequential read sustained:
    * Minimum: 3,000GB/s
    * Recommended: 4,500GB/s
  * Random read, synchronous:
    * Minimum: 250,000 IOPS
    * Recommended: 375,000 IOPS
  * Random read, AIO:
    * Minimum: 500,000 IOPS
    * Recommended: 750,000 IOPS
  * Random write, synchronous:
    * Minimum 100,000 IOPS
    * Recommended: 150,000 IOPS
  * Less than 200µs random read latency, average
* Nodes must pass the Hedera performance test suite performed at installation time

### **Node Operating System:**

* Linux
  * Minimum kernel mainline versions (not distribution version)
    * 6.2.0
    * 6.1.2
    * 6.0.16
    * 5.15.86
  * Actively Supported Long-Term-Support (LTS) 64-bit Linux Distributions
    * Ubuntu LTS 22.04
    * Red Hat Enterprise Linux (RHEL) 8 and 9
    * Oracle Linux 8 and 9

### **Node Software:**

* Docker Engine (`docker-ce` version 20.10.6)
  * Deployed with root privileges
  * Privileged container support enabled (optional)
    * If privileged container support is disabled then host machine must run the Havege Daemon
* Docker Compose (`docker-compose` version 1.29.2)
* IPTables Support (`linux-kernel` version 3.10+)
* Havege Daemon (`haveged` version 1.9.14)
  * If privileged container support is enabled then this requirement is optional
* HashDeep Utilities (`hashdeep` version 4.4)
  * Required for update integrity validation
* Bindplane Collector (`bindplane-collector` version 4+)
  * Required for node software log monitoring
* JQ CLI (`jq` version 1.5+)
  * Required dependency for the Node Management Tools
* GNU CoreUtils (`coreutils` version 8.00+)
  * Required dependency for the Node Management Tools
* cURL CLI (`curl` version 7.58.0+)
  * Required dependency for the Node Management Tools
* InCron Daemon (`incron` version 0.5.12+)
  * Required dependency for the Node Management Tools
  * Required for automated network upgrade
* Rsync CLI (`rsync` version 3.0.0+)
  * Required dependency for the Node Management Tools
  * Required for automated network upgrade
* Node Management Tools (`node-mgmt-tools` version 0.1.0+)
  * Updates deployed via the node upgrade process
  * Must be installed at the following path: `/opt/hgcapp/node-mgmt-tools`
    * The path must be writable and executable by the `hgcadmin` user account

### **System User Accounts:**

* _**Node Software Account (mandatory)**_
  * User Specification
    * Name: `hedera`
    * Unix UID: `2000`
    * Group Membership
      * Primary: `hedera`
      * Secondary: `admin` or `wheel` _(depending on Linux distribution)_
    * Permissions:
      * Read, Write, and Execute Access to the entire `/opt/hgcapp` folder tree
  * Group Specification
    * Name: `hedera`
    * Unix GID: `2000`

{% hint style="info" %}
**Note:** Reference Configurations available in Appendices B, C, D
{% endhint %}

### Proxy

Access to the node via public APIs must be mediated by an in-line proxy. Below are the specifications for establishing this proxy.

* 2-core x86/x64 CPU
* 2GB RAM
* 100GB SSD storage
* 200Mb/s sustained internet network connectivity with public static IP address
* Supported Docker (Hedera to provide Docker image with HAProxy)

### Network Connectivity

Node Connectivity

* 1Gbps internet connectivity – sustained (not burstable)
  * Unmetered preferred
  * Deployed with firewalled access to other mainnet consensus nodes
* Node deployed in dedicated (isolated) DMZ network
  * Static IP (FQDN is not supported)
  * TCP Port 50111 open to 0.0.0.0/0
  * TCP Port 50211 open to 0.0.0.0/0
  * TCP Port 50212 open to 0.0.0.0/0
  * TCP Port 80 open egress to 0.0.0.0/0 (for OS package repository connectivity)
  * TCP Port 443 open egress to 0.0.0.0/0 (for OS package repository connectivity)
  * UDP Port 123 open ingress and egress to 0.0.0.0/0 (for NTP pool synchronization of system time)

Proxy Connectivity

* Static IP address (FQDN not supported)
* 200Mb/s internet connectivity
* TCP Port 80 open egress to 0.0.0.0/0 (for OS package repository connectivity)
* TCP Port 443 open egress to 0.0.0.0/0 (for OS package repository connectivity)
* TCP Port 50211 open to 0.0.0.0/0
* TCP Port 50212 open to 0.0.0.0/0

Interface Bonding (optional)

* If using interface bonding, note that mutual TLS is in use, and Layer 3 Policy Based Routing (PBR) with dual-pathways is not supported. Only Layer 2 interface bonding using mode 1 (autonomous ports using active-backup) or mode 4 (LACP 802.3ad active/active) is supported.

### Hosting

* Industry-standard hosting requirements for security and availability
  * Tier 1 Data Center Hosting facility
  * SSAE 16 /18, SOC 2 Type 2 compliant
* Hedera will seek to avoid duplicating hosting providers across Council members

## Network Topology /(Typical Corporate Datacenter Configuration/)

![](../../../../.gitbook/assets/Network-topology.jpg)

## Deployment Steps

The following steps outline the process for Council Members to add their consensus node to the mainnet.

1. Initial contact with Council Member and node hosting entity
   1. Identify key individuals and project managers
   2. Establish regular deployment team meeting cadence
2. Conveyance of technical requirements and discussion of deployment options
3. Node platform acquisition
   1. Hardware or virtual instance
   2. Network connectivity
   3. Hosting facility
4. Configuration of the operating system on platform
   1. Provisioning of accounts as specified
   2. Provisioning of network access (firewall rules/access control lists)
5. Conveyance of credentials to Hedera
   1. Includes any special instructions for permissioned access such as VPNs
   2. Discussion of support and escalation paths between organizations
6. Hedera undertakes configuration review
   1. Platform
   2. Connectivity
7. Deployment of Hedera consensus node software and required supporting libraries
8. Add connection configuration for a Hedera performance testnet
   1. Hedera executes functional, stability and performance tests for all network services
9. Review of test results and determination of preparedness for mainnet connectivity
   1. Review key management documentation related to Council Member's accounts including: fee account, proxy staking account, et al.
   2. Update private keys using provided tools
10. Schedule mainnet connection
11. Mainnet live
