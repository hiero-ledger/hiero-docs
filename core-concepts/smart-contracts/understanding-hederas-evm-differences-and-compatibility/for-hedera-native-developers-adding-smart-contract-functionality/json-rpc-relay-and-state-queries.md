# JSON-RPC Relay and State Queries

## Overview

Hedera’s JSON-RPC relay provides compatibility with standard Ethereum JSON-RPC methods but is tailored to Hedera’s unique architecture and state management model. This page outlines key differences and practical guidance for developers adding smart contract functionality to Hedera-native applications. The content emphasizes adapting workflows for Hedera's consensus-driven model, understanding JSON-RPC’s behavior on Hedera, and leveraging tools like mirror nodes effectively.

***

## **Key Differences in JSON-RPC Behavior on Hedera**

Hedera’s JSON-RPC relay acts as a compatibility layer, enabling EVM-based tooling to interact with Hedera. While it mirrors the standard Ethereum JSON-RPC API structure, its behavior reflects Hedera’s unique architecture:

<table><thead><tr><th width="181">Feature</th><th>Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>State Management</strong></td><td>No Merkle Patricia Trie. For RPC block data requests, it returns the root hash of an empty Merkle trie.</td><td>Uses a Merkle Patricia Trie for <code>stateRoot</code>, enabling direct historical state verification.</td></tr><tr><td><strong>Historical Data</strong></td><td>Use mirror nodes to retrieve historical events, balances, and transaction details.</td><td>Historical data can be queried directly using Ethereum RPC methods like <code>eth_getBlockByNumber</code>.</td></tr><tr><td><strong>Testing Features</strong></td><td>Does not support contract snapshot features.</td><td>Supports snapshots for fast and modular testing.</td></tr></tbody></table>

***

## Contract Interactions

* Use methods like `eth_call` and `eth_sendTransaction` to interact with deployed contracts via the JSON-RPC relay.
* Fetch historical states or balances using mirror node REST APIs, as Hedera does not use a Merkle Patricia Trie.

### **Example**&#x20;

#### `eth_call` Request

```bash
curl -X POST \
     -H "Content-Type: application/json" \
     -d '{
          "jsonrpc": "2.0",
          "method": "eth_call",
          "params": [
              {
                  "to": "0x1234567890abcdef1234567890abcdef12345678", 
                  "data": "0x6d4ce63c"
              },
              "latest"
          ],
          "id": 1
     }' \
     https://testnet.hashio.io/api
```

#### Querying historical balances

{% code overflow="wrap" %}
```bash
curl -X GET \
     -H "Content-Type: application/json" \
     "https://testnet.mirrornode.hedera.com/api/v1/accounts/0.0.123/balances?timestamp=1672549200"
```
{% endcode %}

***

## Additional Resources

* [**Mirror Node REST API Documentation**](../../../../sdks-and-apis/rest-api.md)
* [**JSON-RPC Methods on Hedera**](https://github.com/hashgraph/hedera-json-rpc-relay/blob/main/docs/rpc-api.md)
