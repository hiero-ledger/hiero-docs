# Deploy a Subgraph Using The Graph and Hedera JSON-RPC Relay

In this tutorial, you'll learn how to create and deploy a subgraph using The Graph protocol. By indexing specific network data using user-defined data structures called "subgraphs," developers can easily query the indexed data through a GraphQL API, creating robust backends for dApps. Subgraphs simplify the process of obtaining blockchain/network data for developers building dApps. This approach removes the complexities of interacting directly with the network, allowing developers to focus on building. Although Hedera supports subgraphs, its hosted service is currently unavailable, so we'll need to set up and run a local graph node to deploy our subgraph.

By the end of this tutorial, you'll be able to configure a mirror node, query data from your subgraph using the GraphQL API, and integrate it into your dApp. You'll also have a better understanding of how to define custom data schemas, indexing rules, and queries for your subgraph, allowing you to tailor it to your specific use case.

{% hint style="info" %}
**Note:**  While it is possible to present and interact with HTS tokens in a similar manner as ERC-20/721 tokens, the network is presently unable to capture all the expected ERC-20/721 event logs. In other words, if ERC-like operations are conducted on HTS tokens, not all of them will be captured in smart contract event logging.
{% endhint %}

***

## Prerequisites

- Basic understanding of JavaScript and NPM installed.
- Basic understanding of subgraphs and the [Graph CLI](deploy-a-subgraph-using-the-graph-and-json-rpc.md#graph-cli-installation) installed.
- The deployed Greeter smart contract address from the [Hardhat tutorial](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md).
- The [start block](deploy-a-subgraph-using-the-graph-and-json-rpc.md#find-start-block) number of when the Greeter smart contract was first deployed.
- [Docker](https://www.docker.com/) `>= v20.10.x` installed and open on your machine. Run `docker -v` in your terminal to check the version you have installed.

<details>

<summary>Find start block</summary>

1. Go to HashScan explorer [here](https://hashscan.io/).
2. Enter your public contract address or contract ID in the search bar.
3. Click on the `Create Transaction` ID ([0.0.902@1676712828.922009885](https://hashscan.io/testnet/transaction/1676712839.177574708?tid=0.0.902-1676712828-922009885)).

<img src="../../.gitbook/assets/explorer new 2 (1).png" alt="" data-size="original">

_**Note:** When searching for contract addresses, there are two types with different formats - the **public** smart contract address (0x....) or **contract ID** (0.0.12345)._

</details>

<details>

<summary>Graph CLI installation</summary>

Open your terminal and run the following command:

```bash
npm install -g @graphprotocol/graph-cli
```

Test to see if it was installed correctly by running:

```bash
graph -v
```

_**Note**: The Graph CLI will be installed globally, so you can run the command in any directory._

</details>

***

## Table of Contents

1. [Project Setup](deploy-a-subgraph-using-the-graph-and-json-rpc.md#project-setup)
2. [Project Configuration](deploy-a-subgraph-using-the-graph-and-json-rpc.md#project-configuration)
3. [Deploy Subgraph](deploy-a-subgraph-using-the-graph-and-json-rpc.md#deploy-subgraph)
4. [Code Check](deploy-a-subgraph-using-the-graph-and-json-rpc.md#code-check)
5. [Additional Resources](deploy-a-subgraph-using-the-graph-and-json-rpc.md#additional-resources)

***

## Project Setup

Open a terminal window and navigate to the directory where you want your subgraph project stored. Clone the `hedera-subgraph-example` repo, change directories, and install dependencies:

```bash
git clone https://github.com/hashgraph/hedera-subgraph-example.git
cd hedera-subgraph-example
npm install
```

Rename the `subgraph.template.yaml` file to `subgraph.yaml` before moving on to the next step. The subgraph project structure should look something like this:

```
subgraph-name
└───abis
│   |  IGreeter.json
└───config
│   └──testnet.json
└───graph-node
│   └──docker-compose.yaml
└───src
│   │   mappings.ts
│   package-lock.json
│   package.json
│   README.md
│   schema.graphql
│   subgraph.yaml
```

In the `testnet.json` file, under the `config` folder, replace the `startBlock` and `Greeter` fields with your start block number and contract address. The JSON file should look something like this:

{% code title="testnet.json" %}

```json
{
    "startBlock": 1050018,
    "Greeter": "0xCc0d40EA9d2Dd16Ab5565ae91b121960d5e19e4e"
}
```

{% endcode %}

***

## Project Configuration

In this step, you will use the `Greeter` contract from the [Hardhat tutorial](deploy-a-smart-contract-using-hardhat-hedera-json-rpc-relay.md) as an example subgraph, to configure four main project files: the subgraph manifest, GraphQL schema, event mappings, and Docker compose configuration. The manifest specifies which events the subgraph will listen for, while mappings map each event emitted by the smart contract into entities that can be indexed.

#### Subgraph Manifest

The subgraph manifest (`subgraph.yaml`) contains important information about your subgraph, such as its name, description, and data sources. To specify the data sources your subgraph will index, you need to define the `dataSources` field in the manifest. It's also recommended to add the start block number to the `startBlock` property to reduce the indexing time. [Here](deploy-a-subgraph-using-the-graph-and-json-rpc.md#find-start-block)'s a guide on how to find the start block number.

1. Add your deployed Greeter public smart contract address to the `address` property.
2. Add your start block number of the deployed contract in the `startBlock` property.

{% code title="subgraph.yaml" %}

```yaml
dataSources:
  - kind: ethereum/contract
    name: Greeter
    network: testnet
    source:
      # Step 1
      address: "0xCc0d40EA9d2Dd16Ab5565ae91b121960d5e19e4e" 
      abi: IGreeter
      # Step 2
      startBlock: 1050018
      
```

{% endcode %}

The `eventHandlers` field specifies how each mapping connects to various event triggers. Whenever an event defined in this section is emitted from your contract, the corresponding mapping function designated as the handler will be executed.

{% code title="subgraph.yaml" %}

```yaml
    eventHandlers:
      - event: GreetingSet(string)
        handler: handleGreetingSet
    file: ./src/mappings.ts
      
```

{% endcode %}

#### GraphQL Schema

The GraphQL schema (`schema.graphql`) defines the structure of the data you want to index in your subgraph. You will need to specify the entity properties that you want to index. For this example, the schema defines a GraphQL entity type called "Greeting" with two entity fields: `id` and `currentGreeting`.

{% code title="schema.graphql" %}

```graphql
type Greeting @entity {
    id: ID!
    currentGreeting: String!
}
```

{% endcode %}

#### Event Mappings

The `mappings.ts` file maps events emitted by your smart contract into entities that can be indexed by a subgraph. It uses _AssemblyScript_ to connect the events to the data schema. AssemblyScript types for entities and events can be generated in the terminal (by running the `codegen` command) and imported into the mappings file. This allows easy access to the event object's properties in the code editor.

{% code title="mappings.ts" %}

```typescript
import { GreetingSet} from '../generated/Greeter/IGreeter';
import {Greeting} from "../generated/schema";


export function handleGreetingSet(event: GreetingSet): void {
	// Entities can be loaded from the store using a string ID; this ID
	// needs to be unique across all entities of the same type
	let entity = Greeting.load(event.transaction.hash.toHexString());

	// Entities only exist after they have been saved to the store;
	// `null` checks allow to create entities on demand
	if (!entity) {
		entity = new Greeting(event.transaction.hash.toHex());
	}

	// Entity fields can be set based on event parameters
	entity.currentGreeting = event.params._greeting;

	// Entities can be written to the store with `.save()`
	entity.save();
}

```

{% endcode %}

#### Graph Node Configuration

To connect a local graph node to a remote network, such as testnet, mainnet, or previewnet, use a [docker-compose](https://github.com/graphprotocol/graph-node/tree/master/docker#docker-compose) setup. The API endpoint that connects the graph node to the network is specified within the `environment` object of the `docker-compose.yaml` file [here](https://github.com/hashgraph/hedera-subgraph-example/blob/main/graph-node/docker-compose.yaml). Add the API endpoint URL in the `ethereum` field in the `environment` object. For this tutorial, we will use the [Hashio Testnet ](https://swirldslabs.com/hashio/)instance of the Hedera JSON-RPC relay, but _any_ [JSON-RPC provider](../../core-concepts/smart-contracts/deploying-smart-contracts/json-rpc-relay.md) supported by the community can be used.

This is what the `ethereum` field should look like after you enter your API endpoint URL:

```yaml
ethereum: 'testnet:https://testnet.hashio.io/api
```

_**Note:** For more info on how to set up an indexer, check out_ T\_he Graph\_ [_docs_](https://thegraph.com/docs/en/) _and the_ [_official graph-node GitHub repository_](https://github.com/graphprotocol/graph-node)_. For a full subgraph project example, check out_ [_this_](https://github.com/hashgraph/hedera-json-rpc-relay/tree/main/tools/subgraph-example) _repo._

***

## Deploy Subgraph

In this step, you will create the subgraph and deploy it to your local graph node. If everything runs without errors, your terminal should resemble the console check at the end of each subsection.

#### 1. Start Graph Node

To start your local graph node, have the Docker engine running before executing the below command in your project directory:

```bash
npm run graph-node
```

<details>

<summary>c<strong>onsole check ✅</strong></summary>

The first time you run the command:

```bash
Creating graph-node_postgres_1 ... done
Creating graph-node_ipfs_1     ... done
Creating graph-node_graph-node_1 ... done
```

What your console will return if you run the command more than once:

```bash
[+] Running 3/0
 ⠿ Container graph-node-postgres-1    Running     0.0s
 ⠿ Container graph-node-ipfs-1        Running     0.0s
 ⠿ Container graph-node-graph-node-1  Running     0.0s
```

</details>

#### 2. Generate Types

In the same directory, run the following command to generate AssemblyScript types for entities and events:

```bash
graph codegen
```

<details>

<summary>c<strong>onsole check ✅</strong></summary>

```
  Skip migration: Bump manifest specVersion from 0.0.2 to 0.0.4
✔ Apply migrations
✔ Load subgraph from subgraph.yaml
  Load contract ABI from abis/IGreeter.json
✔ Load contract ABIs
  Generate types for contract ABI: IGreeter (abis/IGreeter.json)
  Write types to generated/Greeter/IGreeter.ts
✔ Generate types for contract ABIs
✔ Generate types for data source templates
✔ Load data source template ABIs
✔ Generate types for data source template ABIs
✔ Load GraphQL schema from schema.graphql
  Write types to generated/schema.ts
✔ Generate types for GraphQL schema

Types generated successfully
```

</details>

You should have a new folder named `generated` in your project directory. This is what your updated subgraph project structure should look like:

```
subgraph-name
└───abis
│   |  IGreeter.json
└───config
│   └──testnet.json
└───generated
│   └───Greeter
│       │   IGreeter.ts
│   │   schema.ts
└───graph-node
│   └──docker-compose.yaml
└───src
│   │   mappings.ts
│   package-lock.json
│   package.json
│   README.md
│   schema.graphql
│   subgraph.yaml
```

#### 3. Create and Deploy

To create and deploy your subgraph to your local graph node, run:

```bash
// create the subgraph
npm run create-local
```

<details>

<summary>c<strong>onsole check ✅</strong></summary>

```bash
> hedera-subgraph-repo-example@1.0.0 create-local
> graph create --node http://localhost:8020/ Greeter

Created subgraph: Greeter
```

</details>

```bash
// deploy the subgraph
npm run deploy-local
```

When you run the `deploy-local` command, your console will prompt you to provide a `Version Label`. Enter any version number you'd like. This is just a way to keep track of different versions of your subgraph. For instance, if you started with version v0.0.1 today, but then made some changes and wanted to deploy an upgraded version, you bump up the version number to v0.0.2.

_For example: ✔ Version Label (e.g. v0.0.1) · <mark style="background-color:yellow;">v0.0.1</mark>_

<details>

<summary>c<strong>onsole check ✅</strong></summary>

```bash
> hedera-subgraph-repo-example@1.0.0 deploy-local
> graph deploy --node http://localhost:8020/ --ipfs http://localhost:5001 Greeter

✔ Version Label (e.g. v0.0.1) · v0.0.1
✔ Apply migrations
✔ Load subgraph from subgraph.yaml
  Compile data source: Greeter => build/Greeter/Greeter.wasm
✔ Compile subgraph
  Copy schema file build/schema.graphql
  Write subgraph file build/Greeter/abis/IGreeter.json
  Write subgraph manifest build/subgraph.yaml
✔ Write compiled subgraph to build/
  Add file to IPFS build/schema.graphql
                .. QmVtZMzbjU6QHEFfrCJ5NhbP5vUNrukaussxXZ4Esf3qCm
  Add file to IPFS build/Greeter/abis/IGreeter.json
                .. QmZQbrdhaR2p2EZR6raiLbpgX5hjKW4S5cDgy1VvHKmjtH
  Add file to IPFS build/Greeter/Greeter.wasm
                .. QmYz3qFZ4KHiHXhgbTKFxbCNvE9Serhq8yvGuJPK12K5qf
✔ Upload subgraph to IPFS

Build completed: QmbGuuuqtEEqFxjdwSdhiKKpb4GCzqbh3oASAnVVEXRoVW

Deployed to http://localhost:8000/subgraphs/name/Greeter/graphql

Subgraph endpoints:
Queries (HTTP):     http://localhost:8000/subgraphs/name/Greeter
```

After the subgraph is successfully deployed, open the [GraphQL playground](http://localhost:8000/subgraphs/name/Greeter/graphql?query=%0A++++%23%0A++++%23+Welcome+to+The+GraphiQL%0A++++%23%0A++++%23+GraphiQL+is+an+in-browser+tool+for+writing%2C+validating%2C+and%0A++++%23+testing+GraphQL+queries.%0A++++%23%0A++++%23+Type+queries+into+this+side+of+the+screen%2C+and+you+will+see+intelligent%0A++++%23+typeaheads+aware+of+the+current+GraphQL+type+schema+and+live+syntax+and%0A++++%23+validation+errors+highlighted+within+the+text.%0A++++%23%0A++++%23+GraphQL+queries+typically+start+with+a+%22%7B%22+character.+Lines+that+start%0A++++%23+with+a+%23+are+ignored.%0A++++%23%0A++++%23+An+example+GraphQL+query+might+look+like%3A%0A++++%23%0A++++%23+++++%7B%0A++++%23+++++++field%28arg%3A+%22value%22%29+%7B%0A++++%23+++++++++subField%0A++++%23+++++++%7D%0A++++%23+++++%7D%0A++++%23%0A++++%23+Keyboard+shortcuts%3A%0A++++%23%0A++++%23++Prettify+Query%3A++Shift-Ctrl-P+%28or+press+the+prettify+button+above%29%0A++++%23%0A++++%23+++++Merge+Query%3A++Shift-Ctrl-M+%28or+press+the+merge+button+above%29%0A++++%23%0A++++%23+++++++Run+Query%3A++Ctrl-Enter+%28or+press+the+play+button+above%29%0A++++%23%0A++++%23+++Auto+Complete%3A++Ctrl-Space+%28or+just+start+typing%29%0A++++%23%0A++), where you can execute queries and fetch indexed data.

</details>

***

## Code Check ✅

<details>

<summary>subgraph.yaml</summary>

```yaml
specVersion: 0.0.4
description: Graph for Greeter contracts
repository: https://github.com/hashgraph/hedera-subgraph-example
schema:
  file: ./schema.graphql
dataSources:
  - kind: ethereum/contract
    name: Greeter
    network: testnet
    source:
      address: "0xCc0d40EA9d2Dd16Ab5565ae91b121960d5e19e4e"
      abi: IGreeter
      startBlock: 1050018
    mapping:
      kind: ethereum/events
      apiVersion: 0.0.6
      language: wasm/assemblyscript
      entities:
        - Greeting
      abis:
        - name: IGreeter
          file: ./abis/IGreeter.json
      eventHandlers:
        - event: GreetingSet(string)
          handler: handleGreetingSet
      file: ./src/mappings.ts
```

</details>

<details>

<summary>docker-compose.yaml</summary>

**docker-compose.yaml**

```yaml
version: '3'
services:
  graph-node:
    image: graphprotocol/graph-node:v0.27.0
    ports:
      - '8000:8000'
      - '8001:8001'
      - '8020:8020'
      - '8030:8030'
      - '8040:8040'
    depends_on:
      - ipfs
      - postgres
    extra_hosts:
      - host.docker.internal:host-gateway
    environment:
      postgres_host: postgres
      postgres_user: 'graph-node'
      postgres_pass: 'let-me-in'
      postgres_db: 'graph-node'
      ipfs: 'ipfs:5001'
      ethereum: 
      GRAPH_LOG: info
      GRAPH_ETHEREUM_GENESIS_BLOCK_NUMBER: 1
  ipfs:
    image: ipfs/go-ipfs:v0.10.0
    ports:
      - '5001:5001'
    volumes:
      - ./data/ipfs:/data/ipfs
  postgres:
    image: postgres
    ports:
      - '5432:5432'
    command:
      [
          "postgres",
          "-cshared_preload_libraries=pg_stat_statements"
      ]
    environment:
      POSTGRES_USER: 'graph-node'
      POSTGRES_PASSWORD: 'let-me-in'
      POSTGRES_DB: 'graph-node'
      PGDATA: "/data/postgres"
    volumes:
      - ./data/postgres:/var/lib/postgresql/data
```

</details>

#### _Congratulations! You've successfully deployed a subgraph to your local graph node!_&#x20

Once the node finishes indexing, you can access the GraphQL API at: [http://localhost:8000/subgraphs/name/Greeter](http://localhost:8000/subgraphs/name/Greeter)

Follow the steps below to execute the query and fetch the indexed data from the subgraph's entities:

1. Enter the following GraphQL query into the left column of the playground _(see Step 1 in the screenshot below)_:

```graphql
{
  greetings {
    id
  currentGreeting
  }
}
```

2. Execute the query by clicking on the play button at the top of the playground _(see Step 2 in the screenshot below)._
3. The query returns the indexed data from the subgraph's entities on the right column of the playground _(see Step 3 in the screenshot below)_:

```graphql
{
  "data": {
    "greetings": [
      {
        "id": "0xe30c4a439ffbcf4a7e9f3083ec07cc056f456770d080f2f08cc546a399d71516",
        "currentGreeting": "initial_msg"
      }
    ]
  }
}
```

<figure><img src="../../.gitbook/assets/graphql playground 4.png" alt=""><figcaption><p>GraphQL Playground</p></figcaption></figure>

#### **Congratulations! 🎉 You have successfully learned how to deploy a Subgraph using The Graph Protocol and JSON-RPC. Feel free to reach out on** [**Discord**](https://hedera.com/discord) **if you have any questions!**

***

## Additional Resources

**➡** [**Project Repository**](https://github.com/hashgraph/hedera-subgraph-example)

**➡** [**Subgraph Example**](https://github.com/hashgraph/hedera-json-rpc-relay/tree/main/tools/subgraph-example)

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://hashnode.com/@theekrystallee">Hashnode</a></p></td><td><a href="https://github.com/theekrystallee">https://github.com/theekrystallee</a></td></tr><tr><td align="center"><p>Editor: Simi, Sr. Software Manager</p><p><a href="https://github.com/SimiHunjan">GitHub</a> | <a href="https://www.linkedin.com/in/shunjan/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/shunjan/">https://www.linkedin.com/in/shunjan/</a></td></tr><tr><td align="center"><p>Editor: Georgi, Sr Software Dev (LimeChain) </p><p><a href="https://github.com/georgi-l95">GitHub</a> | <a href="https://www.linkedin.com/in/georgi-dimitorv-lazarov/">LinkedIn</a></p></td><td><a href="https://github.com/georgi-l95">https://github.com/georgi-l95</a></td></tr></tbody></table>
