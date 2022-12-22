---
description: >-
  The Hedera Mainnet is currently comprised of permissioned consensus nodes
  operated by the Hedera Governing Council
---

# Node Requirements

The following is provided to help Hedera Governing Council members deploy their permissioned mainnet consensus node. Please note, this information is not intended to apply to Hedera's transition to a permissionless network.

## Minimum Node Requirements

Currently, the Hedera Mainnet will perform at a rate determined by the lowest-performing node. To ensure a common level of performance minimum hardware, connectivity and hosting requirements have been defined for the initial permissioned, Governing Council nodes.

{% hint style="warning" %}
To ensure accurate conformity with the minimum requirements, please provide node hardware, connectivity, and hosting details to Hedera prior to purchase (devops@hedera.com).
{% endhint %}

Chassis (hardware nodes only)

* Dual Power - 1600W minimum, 2000W recommended

All nodes

Network Connectivity: Single 1-Gigabit / 10-Gigabit Ethernet (physical connectivity subject to network infrastructure)

24-core or better CPU hyperthreaded (48 threads) - Intel Xeon Silver class or higher / AMD EPYC 74xx class or higher Virtual hosts must have 48vCPU (single threaded)

256 GB PC4-21300 2666MHz DDR4 ECC Registered DIMM

2 x 240GB SSD with RAID 1 for OS Storage

5TB SSD NVMe usable storage

* Single Drive recommended
* Endurance / Mixed Use (Not Read Intensive)

Storage performance specifications (within 10% of performance parameters below)

* Sequential read: 6200 MB/s
* Random read: 1.08M IOPS
* Sequential write: 3000 MB/s
* Random write: 170K IOPS

Considerations for future expansion (hardware based deployments):

* Chassis should be [Nvidia Tesla V100 PCIe certified](https://www.nvidia.com/en-us/data-center/tesla/tesla-qualified-servers-catalog/)
* 1x Nvidia Tesla V100 PCIe 16GB/32GB GPU

### **Node Operating System:**

* Linux
  * Kernel minimum version 3.10+
  * Actively Support Long-Term-Support Release
    * Ubuntu LTS 18.04+
    * RHEL 7/8

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
Reference Configurations available in Appendices B, C, D
{% endhint %}

### Proxy

Access to the node via public APIs must be mediated by an in-line proxy. Below are the specifications for establishing this proxy.

* 2-CPU
* 2GB RAM
* 100GB storage
* 200Mb/S internet network connectivity with public static IP address
* Docker 18 or higher (Hedera to provide Docker image with HAProxy)

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

* Industry standard hosting requirements for security and availability
  * Tier 1 Data Center Hosting facility
  * SSAE 16 /18, SOC 2 Type 2 compliant
* Hedera will seek to avoid duplicating of hosting providers across Council members

### Software & Installation

* Any 64 bit Long Term Support (LTS) Linux distribution
  * Ubuntu
  * Red Hat Enterprise
  * Debian
  * BSD not supported
  * CentOS deprecated for 2022

## Network Topology /(Typical Corporate Datacenter Configuration/)

![](<../../../.gitbook/assets/Network-topology (1) (1) (1) (1) (1).jpg>)

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
   1. Review key management documentation as it relates to Council Member's accounts including: fee account, proxy staking account, et al.
   2. Update private keys using provided tools
10. Schedule mainnet connection
11. Mainnet live
