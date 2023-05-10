# Get Started with the Hedera Consensus Service Fabric Plugin

{% hint style="warning" %}
You must have a basic understanding of the Hyperledger Fabric network, its key concepts, first-network sample, and transaction flow. Please visit the Hyperledger Fabric [docs](https://hyperledger-fabric.readthedocs.io/en/release-2.0/) to familiarize yourself with these concepts if you have not done so already.
{% endhint %}

## Background

Hyperledger Fabric is one of the most popular private/permissioned enterprise blockchain frameworks available today. Fabric's modular architecture approach enables users to specify network components like network members and choice of consensus protocol (ordering service).

In this tutorial, you will create a Hyperledger Fabric network that leverages the Hedera Consensus Service Fabric plug-in to use Hedera as the ordering service via the first-network sample. The HCS Hyperledger Fabric network will be composed of two organizations. Each organization will host two peer nodes. The two organizations will privately communicate and transact within a Hyperledger Fabric channel.

If you already have experience with the Hyperledger Fabric network and the first-network sample, you can skip down to the requirements section to get started.

## Transaction Flow

1. A client application creates a transaction proposal and sends it to Hyperledger Fabric network peers. The transaction proposal is endorsed (signed) by the Hyperledger Fabric network peers. The endorsed transaction proposal is sent back to the client application.
2. Client application submits the endorsed transaction to the Hyperledger Fabric network HCS ordering node. That Hyperledger Fabric ordering node interacts with the HCS fabric plugin as the ordering service
3. Fabric transactions are fragmented into many messages and submitted to the Hedera network against a particular topic ID
4. Hedera mainnet nodes timestamp and order the fragmented messages
5. Hedera mirror nodes read the fragmented messages in consensus order from the Hedera mainnet
6. Hedera mirror node relays the ordered messages back to the Hyperledger Fabric ordering node
7. Hyperledger Fabric ordering nodes reassembles the fragmented messages into fabric transactions, form fabric blocks, and communicate the blocks to the Hyperledger Fabric peer nodes
8. Hyperledger Fabric peer nodes update to have the same world state

![](../../../.gitbook/assets/hcs\_fabric.png)

## Concepts

A basic description of the concepts for the purposes of this tutorial.

**Client Application**

A client application communicates to a Hyperledger Fabric peer node to submit a transaction proposal and collects the endorsed (signed) transaction proposal. The transaction proposal may initiate a modification of the current state of the distributed ledger.

**Hedera Consensus Service Fabric Plugin**

A consensus service plugin for the Hyperledger Fabric network that leverages the Hedera Consensus Service to order transactions using the unique properties of the hashgraph consensus algorithm that powers the Hedera network.

**Hedera Mirror Node**

A Hedera mirror node reads the ordered messages from a Hedera mainnet node and communicates that information back to the Hyperledger Fabric network. Each message has the following information:

* ConsensusTimestamp
* Message Body
* Topic Running Hash
* Topic Sequence Number

**Hedera Mainnet Node**

A Hedera mainnet node receives the Fabric transaction in fragmented message transactions from the HCS Hyperledger ordering node. That mainnet node gossips the transactions to the other mainnet nodes which collectively assigns the transactions a consensus timestamp and order within the corresponding topic.

**fabric-hcs Repository**

The project that contains all the necessary files to run the HCS Hyperledger Fabric network sample.

**Hyperledger Fabric Network**

The Hyperledger Fabric network is composed of peer nodes, ordering nodes, chaincode (smart contracts), organizations, channels, a distributed ledger and an ordering service.

**Hyperledger Fabric Network Peers**

Peers are nodes in the Hyperledger Fabric network that host instances of the ledger and instances of the chaincode. Client applications interact with the peer nodes to endorse a transaction proposal.

**Hyperledger Fabric Channels**

Channels are a mechanism by which organizations can communicate and transact privately with one another.

**Hyperledger Fabric Organizations**

Organizations are participants that would like to communicate and transact privately with one another.

**Hyperledger Fabric Orderer**

A node that orders the transactions received from a client application (also known as an "ordering node"). In this case, the ordering node interacts with the HCS fabric plugin as the ordering service.

## Requirements:

{% hint style="info" %}
If you would like to run the sample using a virtual environment, please follow the instructions [here](virtual-environment-set-up.md).
{% endhint %}

### Hedera Consensus Service

* Testnet account ID and private key
  * Please follow the instructions [here](../../../networks/testnet/testnet-access.md)

### pluggable-hcs Repository

* You will be directed to clone this [repository](https://github.com/hyperledger-labs/pluggable-hcs) in the outlined steps below

{% hint style="info" %}
The HCS Fabric plugin supports Fabric ordering service 2.0 and is compatible with older versions peers.
{% endhint %}

### Hyperledger Fabric Network

Download and install the following if you do not already have them on your computer.

* Git
  * [Download Git](https://git-scm.com/downloads)
  * Check to see if you have it installed from your terminal: `git --version`
* cURL
  * [Download cURL](https://curl.haxx.se/download.html)
  * Check to see if you have it installed from your terminal: `curl --version`
* wget
  * Install from your terminal (MacOS):`brew install wget`
  * Check to see if you have it installed from your terminal: `wget --version`
* Docker and Docker Compose
  * [Download Docker](https://www.docker.com/get-docker)
  * Docker version 17.06.2-ce or greater is required
  * Check to see if you have it installed from your terminal: `docker --version && docker-compose --version`
* Go Programming Language
  * [Download Go](https://golang.org/dl/)
  * Go version 1.13.x is required
  * Check to see if you have it installed from your terminal: `go version`
* make
  * Install from your terminal (MacOS): `brew install make`
  * Check to see if you have installed from your terminal: `make --version`
* gcc
  * Check to see if you have it installed from your terminal: `gcc --version`

Additionally, you may reference Hyperledger Fabric's documentation for the [prerequisites](https://hyperledger-fabric.readthedocs.io/en/release-2.0/prereqs.html).

### Terminal/IDE

You should be able to use the commands provided in this tutorial in terminal prompt or IDE of choice.

## 1. Clone the [pluggable-hcs](https://github.com/hyperledger-labs/pluggable-hcs) Project

* Open your terminal or favorite IDE
* Enter the following commands to set-up your environment variables (**required**)
  * Optionally you can make these settings permanent by placing them in the appropriate startup file, such as your personal `~/.bashrc` file if you are using the `bash` shell under Linux.

```
$ export GOPATH=$HOME/go
$ export PATH=$PATH:$GOPATH/bin
```

* Create a **hyperledger** directory and navigate into that directory

```
$ mkdir -p $GOPATH/src/github.com/hyperledger && cd $GOPATH/src/github.com/hyperledger
```

* Clone the **pluggable-hcs** repository and rename it to **fabric**
* You **must** rename the folder to **fabric** otherwise you will run into issues in the following steps

```
$ git clone https://github.com/hyperledger-labs/pluggable-hcs fabric
$ cd fabric
```

* Confirm you are on the main branch

```
$ git branch
```

⭐ You have now successfully set your Go path variables and installed the pluggable-hcs/fabric repository.

## 2. Build Fabric Binaries and Docker Images

* cd to the **fabric** directory if you are not there already from your terminal or favorite IDE

```
$ cd fabric
```

* Follow the commands below to build the required fabric binaries and docker images
  * Note: This process may take a few minutes to complete
  * Note: Newer versions of Docker enable gRPC FUSE file sharing implementation by default which result in a failure. Switch to osxfs (Legacy) in Docker Settings

```
$ make clean
$ make configtxgen configtxlator cryptogen orderer peer docker
```

## 3. Hedera Network & HCS Hyperledger Fabric Orderer Configuration

You will now enter your Hedera testnet account ID and private key information to the relevant configuration files. If you have not previously generated your testnet account, please follow the instructions [here](https://docs.hedera.com/guides/testnet/testnet-access).

* Navigate to the **first-network** directory from your terminal or favorite IDE

```
$ cd first-network
```

* Edit the **hedera\_env.json** file via the terminal or IDE
  * This sets up the Hedera environment and allows you to create topics and submit messages to the Hedera network. The Hedera account entered here pays for the transaction fees associated with creating and submitting messages.
  * If you are using an IDE, you may skip this command and edit the file

```
$ nano hedera_env.json
```

* Enter your Hedera account ID (e.g. 0.0.1234) in the **operatorId** field
* Enter your account private key in the **operatorKey** field

```javascript
{
    "operatorId": "put your testnet account id here",
    "operatorKey": "put your account private key here",
    "nodeId": "",
    "nodeAddress": "",
    "network": {
        "0.testnet.hedera.com:50211": "0.0.3",
        "1.testnet.hedera.com:50211": "0.0.4",
        "2.testnet.hedera.com:50211": "0.0.5",
        "3.testnet.hedera.com:50211": "0.0.6"
    },
    "mirrorNodeAddress": "hcs.testnet.mirrornode.hedera.com:5600"
}
```

* Save the **hedera\_env.json** file
* Open the **orderer.yaml** file from your terminal or favorite IDE
  * Here you will set the configurations for the HCS Hyperledger Fabric orderer node
    * This file configures hcs as the ordering service
    * This also contains the node address and node ID to interact with the Hedera mainnet nodes
    * The Hedera mirror node address to receive ordered transaction from
  * If you are using an IDE, you may skip this command and edit the file

```
$ nano orderer.yaml
```

* Scroll down to the "**SECTION: Hcs**" heading
* Enter your Hedera account ID in the **Operator.Id** field
* Enter your Hedera account private key in the **Operator.PrivateKey.Key** field

```yaml
# operator set for the orderer
Operator:
    Id: Your Hedera testnet account id here
    PrivateKey:
        # type of the private key
        Type: ed25519
        # key string (hex or PEM encoded) or path to the key file
        Key: Your Hedera testnet account private key string here
```

* Save the **orderer.yaml** file

{% hint style="info" %}
Please make sure you have entered your Hedera information correctly with no syntax errors as it will cause issues when trying to run the network later.
{% endhint %}

⭐ You have now successfully set up your configuration variables for the Hedera operator and for the HCS Hyperledger ordering node.

## 4. Run Your Network

In this step you will create your HCS Hyperledger Fabric Network.

Make sure you are within the **first-network** directory before running these commands.

* Enter the following command to start the HCS Hyperledger Fabric network

```
$ ./byfn.sh up -t 20
```

* Enter "Y" to accept the 20 second timeout and CLI delay of 3 seconds

{% hint style="info" %}
Please note it's necessary to set the timeout to 20 seconds since otherwise some checks in the script may fail due to HCS having a larger consensus delay than raft/kafka based ordering service.
{% endhint %}

* The script generates two HCS topics
  * One topic will be for the Hyperledger Fabric system channel
  * One topic will be for the Hyperledger Fabric application channel
  * HCS topics n this example are configured so that anyone can submit messages to them
  * HCS messages can be configured to be private topics where the sender would require the `submitKey` to successfully publish messages to them
* Transactions submitted to either of these channels will be visible from a mirror node [explorer](https://docs.hedera.com/guides/testnet/mirror-nodes)
  * Transactions can be fragmented into smaller chunks resulting in multiple HCS messages for a single transaction

The sample is almost identical with the upstream first-network sample as of 2.0.0 release. In summary, the modifications are:

1. Updated docker compose files to use hcs-based orderers.
2. Added a function in `byfn.sh` to generate a 256-bit AES key for data encryption/decryption between fabric orderers and hedera testnet.
3. Added a function in `byfn.sh` to use the hcscli tool for HCS topic creation and mapping topics to fabric channels.

A successful run will end with the following message:

\`========= All GOOD, BYFN execution completed ===========

\| _**\_| | | | |**_\*\* \\\
**| \_| | | | | | | |\_**\
**\_**| |\*\*\_ | | | | |_| |_\
_|\_\__| |_| \\_| |_\_\__/\`

## 5. Tear down the network

* It is required to run the following command to tear down the network
* If you do not tear down the network and try to restart the network you may run into issues

```
$ ./byfn.sh down
```

## 6. Verify Topics & Messages

* Topics and messages created in this tutorial can be verified on any available mirror node explorer
* At the start of the script, you can see the two HCS topic IDs that were created

`installing hcscli ...`\
`generated HCS topics: 0.0.23419 0.0.23420`\
`0.0.23419 will be used for the system channel, and 0.0.23420 will be used for the application channel`

* Visit a Hedera [mirror node explorer](https://docs.hedera.com/guides/testnet/mirror-nodes) to verify the topics and messages that were created on testnet by searching the two topic IDs
* You will be able to view the application channel and system channel topics and all associated messages from this example
  * A single Fabric transaction sent to an ordering node could result in multiple HCS consensus messages as HCS messages have a 6k message size limit
  * i.e. there may not be a 1:1 correlation between a Fabric transaction and HCS message
* A fabric transaction payload is encrypted by the ordering node therefore the subsequent HCS transaction payload is also encrypted
* All messages on the mirror node explorer will be displayed in encrypted format.

{% hint style="info" %}
Make sure you have selected the testnet network toggle in the explorer as the topics and messages created through this tutorial will not appear on the main network.
{% endhint %}

You have successfully done the following:

* Created a Hyperledger Fabric network using the HCS Fabric plugin as the ordering service
* Verified the topics and messages created in this example network

Running into issues or have suggestions? Visit the developer advocates in [Discord](http://hedera.com/discord) and post your comments to the hedera-consensus-service channel 🤓 .
