# JSON-RPC Relay and EVM Tooling

## **Overview**

Hedera’s JSON-RPC relay provides a familiar interface for EVM developers by supporting standard Ethereum JSON RPC methods. This compatibility means you can use popular EVM development tools (like Hardhat, Truffle, or Foundry)  and wallets (like Metamask) to interact with Hedera’s network. However, Hedera’s unique state management model affects how you retrieve historical data and verify states, requiring a shift in approach from the standard EVM workflow.

***

## **Ethereum RPC API Behavior via JSON-RPC Relay**

On Ethereum, methods like `eth_getBlockByNumber` return the true value of `stateRoot` that enables direct historical state verification. Hedera’s JSON-RPC relay, however, returns the root hash of an empty Merkle trie for the `stateRoot` value for compatibility. Instead of relying on it, you should query Hedera’s mirror nodes for historical states, event logs, and transaction details.

#### **Example JSON-RPC Query Request:**

A request to `eth_getBlockByNumber` returns a `stateRoot`, but it’s not useful for historical verification on Hedera. Instead, use mirror node REST APIs to fetch the necessary historical information.

```bash
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{
          "jsonrpc": "2.0",
          "method": "eth_getBlockByNumber",
          "params": [
              "0x1",
              false
          ],
          "id": 1
     }' \
     https://testnet.hashio.io/api
```

This returns the root hash of an empty Merkle trie for compatibility and not the actual `stateRoot` value.

***

## **Testing Without Snapshots**

Hedera does not support contract snapshot feature commonly used in testing. Instead:

* Use modular test designs, deploying fresh contracts before each test.
* Leverage Hedera’s testnet or previewnet for integration testing.
* Query mirror nodes for state verification over time.
* Maintain isolated accounts for each test to ensure a clean environment.

**Code Example: Modular Testing with Contract Redeployment**

```solidity
pragma solidity ^0.8.0;

contract TestContract {
    uint256 public value;

    function setValue(uint256 _value) public {
        value = _value;
    }

    function reset() public {
        value = 0;
    }
}

contract TestSuite {
    TestContract testContract;

    // Deploy a fresh contract for each test
    function deployNewContract() public {
        testContract = new TestContract();
    }

    // Example test: setValue
    function testSetValue(uint256 _value) public {
        testContract.setValue(_value);
        require(testContract.value() == _value, "Value not set correctly");
    }

    // Example test: reset
    function testReset() public {
        testContract.reset();
        require(testContract.value() == 0, "Value not reset correctly");
    }
}
```

***

## Endpoints <a href="#gossip-methods" id="gossip-methods"></a>

The JSON RPC Relay methods implement a subset of the standard method:

### Gossip Methods <a href="#gossip-methods" id="gossip-methods"></a>

These methods track the head of the chain. This is how transactions make their way around the network, find their way into blocks, and how clients find out about new blocks.

<table><thead><tr><th width="383">Method</th><th>Static Response Value</th></tr></thead><tbody><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_blocknumber">eth_blockNumber</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_sendrawtransaction">eth_sendRawTransaction</a></td><td>N/A</td></tr></tbody></table>

### State Methods <a href="#state_methods" id="state_methods"></a>

Methods that report the current state of all the data stored. The "state" is like one big shared piece of RAM, and includes account balances, contract data, and gas estimations.

<table><thead><tr><th width="380">Method</th><th>Static Response Value</th></tr></thead><tbody><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_getbalance">eth_getBalance</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_getstorageat">eth_getStorageAt</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_gettransactioncount">eth_getTransactionCount</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_getcode">eth_getCode</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_call">eth_call</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_estimategas">eth_estimateGas</a></td><td>Generates and returns an estimate of how much gas is necessary to allow the transaction to complete.</td></tr></tbody></table>

### History Methods <a href="#history_methods" id="history_methods"></a>

Fetches historical records of every block back to genesis. This is like one large append-only file, and includes all block headers, block bodies, uncle blocks, and transaction receipts.

<table><thead><tr><th width="379">Method</th><th>Static Response Value</th></tr></thead><tbody><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_getblocktransactioncountbyhash">eth_getBlockTransactionCountByHash</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_getblocktransactioncountbynumber">eth_getBlockTransactionCountByNumber</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_getunclecountbyblockhash">eth_getUncleCountByBlockHash</a></td><td><code>null</code></td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_getunclecountbyblocknumber">eth_getUncleCountByBlockNumber</a></td><td>0<code>x0</code></td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_getblockbyhash">eth_getBlockByHash</a></td><td>Note that <code>stateRoot</code> value returned is always zero.</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_getblockbynumber">eth_getBlockByNumber</a></td><td>Note that <code>stateRoot</code> value returned is always zero.</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_gettransactionbyhash">eth_getTransactionByHash</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_gettransactionbyblockhashandindex">eth_getTransactionByBlockHashAndIndex</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_gettransactionbyblocknumberandindex">eth_getTransactionByBlockNumberAndIndex</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_gettransactionreceipt">eth_getTransactionReceipt</a></td><td>N/A</td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_getunclebyblockhashandindex">eth_getUncleByBlockHashAndIndex</a></td><td><code>null</code></td></tr><tr><td><a href="https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_getunclebyblocknumberandindex">eth_getUncleByBlockNumberAndIndex</a></td><td><code>null</code></td></tr></tbody></table>

{% hint style="info" %}
**💡&#x20;**_**See the full list of methods**_ [_**here**_](https://github.com/hashgraph/hedera-json-rpc-relay/blob/main/docs/rpc-api.md)_**.**_
{% endhint %}

***

## Supported EVM Development Tools

<table><thead><tr><th width="215">Feature</th><th width="84">web3js</th><th width="83">Truffle</th><th width="78">ethers</th><th width="94">Hardhat</th><th width="112">Remix IDE</th><th>Foundry</th></tr></thead><tbody><tr><td>Transfer HBARS</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td></tr><tr><td>Contract Deployment</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td></tr><tr><td>Can use the contract instance after deploy without re-initialization</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td></tr><tr><td>Contract View Function Call</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td></tr><tr><td>Contract Function Call</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td><td>✅</td></tr><tr><td>Debug Operations*</td><td>❌</td><td>❌</td><td>❌</td><td>❌</td><td>❌</td><td>❌</td></tr></tbody></table>

{% hint style="info" %}
\*1: Debug operations are not supported yet.

**Note**: Development tools usually make a lot of requests to certain endpoints, especially during contract deployment. Be aware of rate limiting when deploying multiple large contracts.

**Note**: Enable [`development mode`](https://github.com/hashgraph/hedera-json-rpc-relay/blob/main/docs/dev-mode.md) to correctly assert revert messages of contract calls with `hardhat-chai-matchers`.

_**💡 See the full list of supported EVM tools**_ [_**here**_](https://github.com/hashgraph/hedera-json-rpc-relay/tree/main/tools)_**.**_
{% endhint %}

***

## Additional Resources

* [**Supported EVM Tooling**](https://github.com/hashgraph/hedera-json-rpc-relay/tree/main/tools)
* [**JSON-RPC Relay Docs**](https://docs.hedera.com/hedera/core-concepts/smart-contracts/json-rpc-relay)
* [**Hedera JSON-RPC Relay Repo**](https://github.com/hashgraph/hedera-json-rpc-relay)
