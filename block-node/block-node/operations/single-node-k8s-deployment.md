# Bare Metal Single Node Kubernetes Deployment

This document provides instructions for deploying the Block Node Server Helm chart in a Single-Node Kubernetes environment.
This setup is ideal for production environments on bare metal or cloud VMs.

## Prerequisites

A server with a supported operating system and sufficient resources to run Kubernetes and
the Block Node Server is required.

For full hardware specifications — including minimum CPU, RAM, disk, and NIC requirements
for both deployment profiles, storage I/O benchmark targets, and network latency
requirements — see the
**[Block Node Hardware Specifications](./block-node-hardware-specifications.md)** document.

Quick reference for minimum mainnet deployments:

|          Profile          |              CPU              |  RAM   | Fast NVMe | Bulk Storage |    NICs     |
|---------------------------|-------------------------------|--------|-----------|--------------|-------------|
| Local Full History (LFH)  | 24c / 48t, ≥ 2.0 GHz, PCIe 4+ | 256 GB | 8 TB      | 100 TB       | 2 × 10 Gbps |
| Remote Full History (RFH) | 24c / 48t, ≥ 2.0 GHz, PCIe 4+ | 256 GB | 8 TB      | —            | 2 × 10 Gbps |

Note: Servers may be acquired from bare metal or cloud providers that offer dedicated
instances. LFH configurations require significant storage and are typically sourced from
bare metal providers, or purchased outright and self-hosted, or colocated.

## Server Provisioning

Once a server has been acquired, it needs to be provisioned with the necessary software components to run Kubernetes
and the Block Node Server.

Assuming a Linux based environment, a node operator has two options at this time
1. [Solo Provisioner](https://github.com/hashgraph/solo-weaver) installation (recommended)
2. Custom provisioner script

### Solo Provisioner

Solo Provisioner (formerly known as Solo Weaver) is a go-based tool to simplify the provisioning of Hiero network components (like the block node) in a
streamlined and automated fashion. For a step-by-step GCP VM walkthrough using Solo Provisioner (including `local`, `previewnet`, and `testnet` profiles), see the [Virtual Machine Single Node Kubernetes Deployment Guide](./solo-weaver-single-node-k8s-deployment.md). For production Tier 1 mainnet, the [Block Node Hardware Specifications](./block-node-hardware-specifications.md) take precedence over the smaller VM sizings used there.

To utilize the Solo Provisioner experience

1. Install Solo Provisioner on the server

```bash
curl -sSL https://raw.githubusercontent.com/hashgraph/solo-weaver/main/install.sh | bash
solo-provisioner --help
```

2. Run the provisioning and install flow
   Follow the [Setup Block Node](https://github.com/hashgraph/solo-weaver/blob/main/docs/quickstart.md#setup-block-node) steps.

### Custom Provisioning Script

A node operator with sufficient knowledge and expertize may desire to create a script that factors in their cloud
provider and business needs when provisioning the machine.

In this case the following recipe steps are suggested as a guide when designing your script
1. Disable Linux Swap
2. Configure [Sysctl for Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/sysctl-cluster/)
3. Setup Bind Mounts
4. Setup [Kubelet](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/) and its Systemd Service
5. Setup [Kubectl](https://kubernetes.io/docs/reference/kubectl/kubectl/)
6. Setup [Helm](https://helm.sh/)
7. Setup [K9s](https://k9scli.io/)
8. Setup [CRI-O](https://cri-o.io/) and its Systemd Service
9. Setup [Kubeadm](https://kubernetes.io/docs/reference/setup-tools/kubeadm/)
10. Initialize Cluster
11. Setup and start [Cilium](https://cilium.io/)
12. Setup [MetalLB](https://metallb.io/)
13. Check ClusterHealth

Note: The script recipe is provided as-is (with no maintenance) and may require further edits by operators depending on
their OS and version. The recipe focuses on setting up k8s, supporting helm based installation and kubectl modification
in addition to metalLB load balancing.

## Installation Steps

With the server provisioned, follow these steps to deploy the Block Node Server on a single-node Kubernetes cluster:

Note 1: Skip steps 1 through 7 if you utilize Solo Provisioner as will install the block node for you.

Note 2: Some steps are marked as intermediate, indicating they will change due to improvements.

1. **ENV variables setup**: Set some helpful environment variables for your deployment in a `.env file:

   ```bash
   NAMESPACE=<insert namepace>
   RELEASE=<insert release name>
   VERSION=<insert latest stable block node GA version>
   POD=${RELEASE}-block-node-server-0
   ```

   A sample `.env` file is provided at [.env.sample](./../../../tools-and-tests/scripts/node-operations/sample.env).

2. (Intermediate) **Automate with Task**: Use the provided [Taskfile.yml](../../../tools-and-tests/scripts/node-operations/Taskfile.yml) to
   streamline the deployment process. The Taskfile includes tasks for installing Helm charts, configuring the Block Node
   Server, and managing the Kubernetes cluster.

3. (Intermediate) **Setup `kubectl` and `helm` environments**:

   ```bash
   task load-kubectl-helm
   ```
4. **Configure Persistent Volume Creation Script**: Create Persistent Volume (PV)s and Persistent Volume Claim (PVC)s
   for Block Node Server data storage.

   Update [./values-overrides/host-paths.yaml](../../../charts/block-node-server/values-overrides/host-paths.yaml) with the appropriate namespace.

   ```bash
   kubectl apply -f ./k8s/single-node/pv-pvc.yaml -n ${NAMESPACE}
   ```
5. **Configure Helm Chart Values**: Customize the Helm chart values for your deployment.

   Update [./lfh-values.yaml](../../../charts/block-node-server/values-overrides/lfh-values.yaml) or [./values-overrides/rfh-values.yaml](../../../charts/block-node-server/values-overrides/rfh-values.yaml) with your specific configuration settings.

6. **Install Block Node Server Helm Chart**: Deploy the Block Node Server using Helm.

   ```bash
   task helm-release
   ```
7. **Verify Deployment**: Check the status of the Block Node Server deployment to ensure it is running correctly.

   ```bash
   kubectl get pods -n ${NAMESPACE}
   kubectl logs ${POD} -n ${NAMESPACE}
   ```

   Expected output should indicate that the Block Node Server is operational.

   ```bash
   # [org.hiero.block.node.app.BlockNodeApp start] Started BlockNode Server : State = RUNNING, Historic blocks =
   ```
8. **Access Block Node Server**: Connect to the Block Node Server using the configured service endpoint.

   Install `grpcurl` if not already installed:

   ```bash
   task setup-grpcurl
   ```

   Install protobuf compiler if not already installed:

   ```bash
   task setup-bn-proto
   ```

   Use `grpcurl` to interact with the Block Node Server:

   ```bash
   grpcurl -plaintext -emit-defaults -import-path block-node-protobuf-<VERSION> -proto block-node/api/node_service.proto -d '{}' <host>:40840 org.hiero.block.api.BlockNodeService/serverStatus
   ```

   Expected output should show the server status:

   ```bash
   # expected response will be, 18446744073709551615 implies -1 which is expected on a new BN
   {
     "firstAvailableBlock": "18446744073709551615",
     "lastAvailableBlock": "18446744073709551615",
     "onlyLatestState": false,
     "versionInformation": null
   }
   ```
9. **Helm Chart Upgrades**: To upgrade the Block Node Server Helm chart to a newer version, update the `VERSION`
   variable in your `.env` file and run:

   ```bash
   task helm-upgrade
   ```
10. **(Caution) Reset Block Node Server Data**: To reset the Block Node Server (clear data and install version), run:

    ```bash
    task reset-upgrade
    ```
11. **Uninstall Block Node Server**: To uninstall the Block Node Server and remove all associated resources, run:

```bash
task clear-release
```

> **See also:** [Resetting and Upgrading the Block Node](./resetting-and-upgrading-the-block-node.md) - covers when and why to reset or upgrade, pre- and post-operation checks, rollback, and troubleshooting that complement the one-line commands above.
