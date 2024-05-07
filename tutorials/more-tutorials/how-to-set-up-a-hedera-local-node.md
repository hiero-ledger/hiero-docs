# How to Set Up a Hedera Local Node

The [**Hedera Local Node**](https://github.com/hashgraph/hedera-local-node) project enables developers to establish their own local network for development and testing. The local network comprises the consensus node, mirror node, [JSON-RPC relay](https://github.com/hashgraph/hedera-json-rpc-relay#readme), and other Hedera products, and can be set up using the CLI tool and Docker. This setup allows you to seamlessly build and deploy smart contracts from your local environment.

By the end of this tutorial, you'll be equipped to run a Hedera local node and generate keys, allowing you to test your projects and deploy projects in your local environment.

***

## Prerequisites

* [Node.js](https://nodejs.org/en) >= v14.x
* [NPM](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) >= v6.14.17\*\*\*\*\*\*
* Minimum 16GB RAM
* [Docker](https://www.docker.com/) >= v20.10.x
* [Docker Compose](https://docs.docker.com/compose/) >= v2.12.2
* Have Docker running on your machine with the correct configurations.

<details>

<summary><a href="https://github.com/hashgraph/hedera-local-node#requirements">Docker configuration 🛠️</a></summary>

Ensure the **`VirtioFS`** file sharing implementation is enabled in the docker settings.

**Note:** The image may look different if you are on a different version

<img src="../../.gitbook/assets/docker-compose-settings.png" alt="" data-size="original">

Ensure the following configurations are set at minimum in Docker **Settings** -> **Resources** and are available for use:

* **CPUs:** 6
* **Memory:** 8GB
* **Swap:** 1 GB
* **Disk Image Size:** 64 GB

**Note:** The image may look different if you are on a different version

<img src="../../.gitbook/assets/docker settings.png" alt="" data-size="original">

</details>

_**\*\***Local node can be run using Docker or NPM but we will use Docker for this tutorial._ [_Here_](https://github.com/hashgraph/hedera-local-node#official-npm-release) _are the installation steps for NPM._

***

## Table of Contents

1. [Set Up Local Node](how-to-set-up-a-hedera-local-node.md#step-1-local-node-setup)
2. [Start Local Node](how-to-set-up-a-hedera-local-node.md#step-2-start-local-node)
3. [Generate Keys](how-to-set-up-a-hedera-local-node.md#step-3-generate-keys)
4. [Stop Local Node](how-to-set-up-a-hedera-local-node.md#step-4-stop-local-node)
5. [Additional Resources](how-to-set-up-a-hedera-local-node.md#additional-resources)

***

## Step 1: Local Node Setup

Open a new terminal and navigate to your preferred directory where your Hedera Local Node project will live. Run the following command to clone the repo and install dependencies to your local machine:

```bash
git clone https://github.com/hashgraph/hedera-local-node.git
cd hedera-local-node
npm install && npm install -g
```

For Windows users: You will need to update the file endings of `compose-network/mirror-node/init.sh` by running this in WSL:

```bash
dos2unix compose-network/mirror-node/init.sh
```

***

## Step 2: Start Local Node

Let's start by initializing and starting your local environment. Make sure Docker is installed and open on your machine before running this command:

```bash
hedera start -d
```

The `hedera start -d` command will start your local node and generate 10 accounts with predefined private keys for each key type. There are three key types: _ECDSA, ECDSA alias, and ED25519._ All of them are usable via HederaSDK. Only Alias ECDSA accounts can be imported into a wallet like Metamask or used in ethers.

<details>

<summary><code>hedera start -d</code></summary>

```
Checking docker compose version...
Applying local config settings...
Successfully applied local config settings
Starting hedera local node in single-node mode...
Stopping the network...
Stopping the docker containers...
Cleaning the volumes and temp files...
Detecting the network...
Starting the network...
Preparing Node...
Importing fees...
Waiting for topic creation...
Generating accounts in synchronous mode...
|-----------------------------------------------------------------------------------------|
|-----------------------------| Accounts list ( ECDSA  keys) |----------------------------|
|-----------------------------------------------------------------------------------------|
|    id    |                            private key                            |  balance |
|-----------------------------------------------------------------------------------------|
| 0.0.1002 - 0xb65eb09db3e76bab4aa06988f445b40d6ef6fae8a8d401ac1693bab9ac927df1 - 10000 ℏ |
| 0.0.1003 - 0x1f5c6e6d1bb629c545d44b21b895b7c2b667f430d27f4998c2e2d36bf1568746 - 10000 ℏ |
| 0.0.1004 - 0xfe7086884f8863f0ccf66fbcd7dfc7485f9ff88d7705e6c55b4e2366161edb89 - 10000 ℏ |
| 0.0.1005 - 0x1c6a05ee1a00e0b7b46d1693fbad7d4d7f2da322d4b7e11e26b2f099d7293ecb - 10000 ℏ |
| 0.0.1006 - 0x1144fb7da2f1f4f2f9cde0d5742a8be8e6bae842f978de3ef65bd8591f2e5fe8 - 10000 ℏ |
| 0.0.1007 - 0x13182eadc0f7ff7624926b194f62fab8b09ff78ca54d5138d1af4131c070459e - 10000 ℏ |
| 0.0.1008 - 0x78fa1228d87f870c6515e6a43acb2fd0dd306f538a41c57c9f90d47a8fd076e1 - 10000 ℏ |
| 0.0.1009 - 0x6c7ac42aa92ea72d1e3eef0a095ecbe7dba1830b83080d068b6d28215140cd61 - 10000 ℏ |
| 0.0.1010 - 0xd91171ea81f8bfe4555b40f6e2997465a785853266ad0ef7f2e1f9843c0e502d - 10000 ℏ |
| 0.0.1011 - 0x4a48ae9ec00c80c811836dd0225f22a0b7fbc44a48dc7db45d8748e036db7353 - 10000 ℏ |
|-----------------------------------------------------------------------------------------|

|--------------------------------------------------------------------------------------------------------------------------------------|
|------------------------------------------------| Accounts list (Alias ECDSA keys) |--------------------------------------------------|
|--------------------------------------------------------------------------------------------------------------------------------------|
|    id    |               public address               |                             private key                            | balance |
|--------------------------------------------------------------------------------------------------------------------------------------|
| 0.0.1012 - 0x67D8d32E9Bf1a9968a5ff53B87d777Aa8EBBEe69 - 0x105d050185ccb907fba04dd92d8de9e32c18305e097ab41dadda21489a211524 - 10000 ℏ |
| 0.0.1013 - 0x05FbA803Be258049A27B820088bab1cAD2058871 - 0x2e1d968b041d84dd120a5860cee60cd83f9374ef527ca86996317ada3d0d03e7 - 10000 ℏ |
| 0.0.1014 - 0x927E41Ff8307835A1C081e0d7fD250625F2D4D0E - 0x45a5a7108a18dd5013cf2d5857a28144beadc9c70b3bdbd914e38df4e804b8d8 - 10000 ℏ |
| 0.0.1015 - 0xc37f417fA09933335240FCA72DD257BFBdE9C275 - 0x6e9d61a325be3f6675cf8b7676c70e4a004d2308e3e182370a41f5653d52c6bd - 10000 ℏ |
| 0.0.1016 - 0xD927017F5a6a7A92458b81468Dc71FCE6115B325 - 0x0b58b1bd44469ac9f813b5aeaf6213ddaea26720f0b2f133d08b6f234130a64f - 10000 ℏ |
| 0.0.1017 - 0x5C41A21F14cFe9808cBEc1d91b55Ba75ed327Eb6 - 0x95eac372e0f0df3b43740fa780e62458b2d2cc32d6a440877f1cc2a9ad0c35cc - 10000 ℏ |
| 0.0.1018 - 0xcdaD5844f865F379beA057fb435AEfeF38361B68 - 0x6c6e6727b40c8d4b616ab0d26af357af09337299f09c66704146e14236972106 - 10000 ℏ |
| 0.0.1019 - 0x6e5D3858f53FC66727188690946631bDE0466B1A - 0x5072e7aa1b03f531b4731a32a021f6a5d20d5ddc4e55acbb71ae202fc6f3a26d - 10000 ℏ |
| 0.0.1020 - 0x29cbb51A44fd332c14180b4D471FBBc6654b1657 - 0x60fe891f13824a2c1da20fb6a14e28fa353421191069ba6b6d09dd6c29b90eff - 10000 ℏ |
| 0.0.1021 - 0x17b2B8c63Fa35402088640e426c6709A254c7fFb - 0xeae4e00ece872dd14fb6dc7a04f390563c7d69d16326f2a703ec8e0934060cc7 - 10000 ℏ |
|--------------------------------------------------------------------------------------------------------------------------------------|

|-----------------------------------------------------------------------------------------|
|-----------------------------| Accounts list (ED25519 keys) |----------------------------|
|-----------------------------------------------------------------------------------------|
|    id    |                            private key                            |  balance |
|-----------------------------------------------------------------------------------------|
| 0.0.1022 - 0x1d0bdde18f1a570a0204297e1cba9b3f90a3c7774ac01bc7dd8d83db436085c0 - 10000 ℏ |
| 0.0.1023 - 0x1c174991c38b78ad063117625587275fa0ee74e7bc72513a6453f1bfd247248f - 10000 ℏ |
| 0.0.1024 - 0x10745c43037fd81cd4ef1524cb2270b7de45485c30f70468a4c3a87a4272271d - 10000 ℏ |
| 0.0.1025 - 0xd19f919d393efbfc6895d52b084d07f0cc95f3cb80345aba76677081c0aea39c - 10000 ℏ |
| 0.0.1026 - 0xb1ef6aa2a56f7ee88c3572d87e53f97b59ec6ff2079b232a9662b29b95c3db6f - 10000 ℏ |
| 0.0.1027 - 0xea54f3665ac84365b1db0eb5b04aab227237d0d0c6e0e80ee773b0ffc6f8b551 - 10000 ℏ |
| 0.0.1028 - 0x15f5ba26f0367b62ee06804bec3347144a0edcc748fa589e609f32462dae389c - 10000 ℏ |
| 0.0.1029 - 0x069ba185244a4ee23e9464fc620d8337527eca2df3df596052bb524fe51811ba - 10000 ℏ |
| 0.0.1030 - 0xd078136746079fb394a6d4442e2be55e9350c5734423835d14926099021d4ee0 - 10000 ℏ |
| 0.0.1031 - 0xd1bd93d037cdb0d53bdc5cf35db51989c1d6e7a08747eecd1ae16ea81fe76fd0 - 10000 ℏ |
|-----------------------------------------------------------------------------------------|

Local node has been successfully started in detached mode.
```

</details>

***

## Step 3: Generate Keys

To generate accounts with random private keys, run the `generate-accounts` command. Specify the number of accounts generated by appending the number to the `hedera start` or `hedera generate-account` command. For example, to generate 5 accounts at startup, run `hedera start 5`.

<details>

<summary><code>hedera generate-accounts 5</code></summary>

```
Generating accounts in synchronous mode...
|-----------------------------------------------------------------------------------------|
|-----------------------------| Accounts list ( ECDSA  keys) |----------------------------|
|-----------------------------------------------------------------------------------------|
|    id    |                            private key                            |  balance |
|-----------------------------------------------------------------------------------------|
| 0.0.1033 - 0xced34a00d3fff542e350a5e61cb41509812bf23ea581f83a0a862c94d8c69704 - 10000 ℏ |
| 0.0.1034 - 0xa4189ab682ba43925ce654ca09800bba86cf8b1b7f889006d5170d95f4fed365 - 10000 ℏ |
| 0.0.1035 - 0xf9106e9841677136c9cbe8c114dab80470ca62a15bfe9c777006bcb114288c22 - 10000 ℏ |
| 0.0.1036 - 0xe3517a9235971be1e1f95e791f3ffd7d753a652799fa11f1ace626036c4db275 - 10000 ℏ |
| 0.0.1037 - 0x636926cf2f6f9fd0a58043c600390eeef0bbed9d4b8a113ea68a8d67f922d04e - 10000 ℏ |
|-----------------------------------------------------------------------------------------|

|--------------------------------------------------------------------------------------------------------------------------------------|
|------------------------------------------------| Accounts list (Alias ECDSA keys) |--------------------------------------------------|
|--------------------------------------------------------------------------------------------------------------------------------------|
|    id    |               public address               |                             private key                            | balance |
|--------------------------------------------------------------------------------------------------------------------------------------|
| 0.0.1038 - 0xaBE90e20f394629e054Bc1E8F1338Fe8ea94F0b5 - 0x444913bd258f764e62db6c87abde7ca52ec22985db8c91b8c3b2b4f2c51775f0 - 10000 ℏ |
| 0.0.1039 - 0x26d941d8E1f6bF9B0F7e5156fA6ff02acEd0DF3E - 0xea25f427caf7029989669f93926b7902dde5361b176b4bc17b8ec0a967beaa0b - 10000 ℏ |
| 0.0.1040 - 0x64001c2d1f3a8d3574435B4F125944018E2E584D - 0xf2deb678a1e67e288d8a128334f41c890e7600b2a5471ecc9a3af4824e3021b7 - 10000 ℏ |
| 0.0.1041 - 0x6bE22CD9D16b64969683B74897E4EBB30c7c30E8 - 0xb9c2480cdbdddb2ecd6e032b87820c29e8791ad4f53b89f829269d856c835819 - 10000 ℏ |
| 0.0.1042 - 0x992d8aD211b28B23589c0b3Fe30de6C90662C4aB - 0x7e8bb0d85a8d80fa2eb2c9f6bd5c9b1a2c2f9f6992c7fffd201c8e81f0ec0000 - 10000 ℏ |
|--------------------------------------------------------------------------------------------------------------------------------------|

|-----------------------------------------------------------------------------------------|
|-----------------------------| Accounts list (ED25519 keys) |----------------------------|
|-----------------------------------------------------------------------------------------|
|    id    |                            private key                            |  balance |
|-----------------------------------------------------------------------------------------|
| 0.0.1043 - 0xd4917e152ca922b8bfbafffc3486512ae25ec0a75b05c44f517b11cd12fd949b - 10000 ℏ |
| 0.0.1044 - 0xbaeec69382fbb43e4d521b3d8717c9cba610a1fbcaededaaf4408c3138a683ae - 10000 ℏ |
| 0.0.1045 - 0x1f5c4b2efd3c36d29e9d2e16a825abd001f99bff2388bb8c6011cd5f956023c9 - 10000 ℏ |
| 0.0.1046 - 0x1976acdd5e71ce7e8db4cb0aa112fa1c16876155f0f20b9b7029916073f1d67f - 10000 ℏ |
| 0.0.1047 - 0x6e29f48b11ffc77e277f0500d607b35956da58f1ed30aad003fb1846bfffc483 - 10000 ℏ |
|-----------------------------------------------------------------------------------------|
```

</details>

{% hint style="info" %}
**Please note**: Since the first 10 accounts generated are with predefined private keys, if you need 5 generated with random keys, you will run `hedera start 15`. The same rule applies when you use the `hedera generate-accounts` command.
{% endhint %}

Grab any of the account private keys generated from the _**Alias ECDSA keys Accounts list**_. This will be used as the `LOCAL_NODE_OPERATOR_PRIVATE_KEY` environment variable value in your `.env` file of your project.

***

## Step 4: Stop Local Node

To stop your local node, you can run the `hedera stop` command to stop and remove the Docker container volumes and clean the manually generated files. If you want to keep any files created manually in the working directory, please save them before executing this command.

<details>

<summary><code>hedera stop</code></summary>

```
Stopping the network...
Stopping the docker containers...
Cleaning the volumes and temp files...
```

</details>

Alternatively, run `docker compose down -v; git clean -xfd; git reset --hard` to stop the local node and reset it to its original state.

<details>

<summary><code>docker compose down -v; git clean -xfd; git reset --hard</code></summary>

```bash
[+] Running 27/27
 ✔ Container mirror-node-web3           Removed            3.5s 
 ✔ Container json-rpc-relay-ws          Removed           10.8s 
 ✔ Container mirror-node-monitor        Removed            3.7s 
 ✔ Container relay-cache                Removed            0.9s 
 ✔ Container prometheus                 Removed            0.9s 
 ✔ Container record-sidecar-uploader    Removed            0.0s 
 ✔ Container grafana                    Removed            0.9s 
 ✔ Container hedera-explorer            Removed           10.4s 
 ✔ Container json-rpc-relay             Removed           10.7s 
 ✔ Container account-balances-uploader  Removed            0.1s 
 ✔ Container envoy-proxy                Removed            1.0s 
 ✔ Container mirror-node-grpc           Removed            2.7s 
 ✔ Container mirror-node-rest           Removed           10.4s 
 ✔ Container network-node               Removed           10.8s 
 ✔ Container mirror-node-importer       Removed           10.4s 
 ✔ Container record-streams-uploader    Removed            0.0s 
 ✔ Container haveged                    Removed            0.0s 
 ✔ Container mirror-node-db             Removed            0.3s 
 ✔ Container minio                      Removed            0.0s 
 ✔ Volume prometheus-data               Removed            0.0s 
 ✔ Volume minio-data                    Removed            0.0s 
 ✔ Volume mirror-node-postgres          Removed            0.1s 
 ✔ Volume grafana-data                  Removed            0.2s 
 ✔ Network network-node-bridge          Removed            0.1s 
 ✔ Network hedera-local-node_default    Removed            0.2s 
 ✔ Network cloud-storage                Removed            0.2s 
 ✔ Network mirror-node                  Removed            0.2s 
Removing .husky/_/
Removing network-logs/
Removing node_modules/
HEAD is now at ......
```

</details>

***

## Additional Resources

**➡** [**Hedera Local Node Repository**](https://github.com/hashgraph/hedera-local-node#readme)

**➡** [**Hedera Local Node CLI Tool Commands**](https://github.com/hashgraph/hedera-local-node#using-hedera-local)

**➡** [**Hedera Local Node Docker Setup** ](https://www.youtube.com/watch?v=KOhzu6ftmbY)**\[Video Tutorial]**
