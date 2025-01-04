# EVM Archive Node Queries

## Introduction to EVM Archive Node Queries

[HIP-584](https://hips.hedera.com/hip/hip-584) enhances Hedera Mirror Nodes with extended EVM execution capabilities, allowing developers to perform gas-free smart contract queries, estimate gas usage, and simulate EVM transactions without committing state changes. These enhancements empower developers to:

* **Perform Gas-Free Smart Contract Queries:** Retrieve data from smart contracts without incurring gas costs.
* **Estimate Gas Usage:** Determine the gas required for executing specific contract functions.
* **Simulate EVM Transactions:** Test transactions that involve state changes without committing those changes to the blockchain.

{% hint style="info" %}
This guide uses real examples of interactions with smart contracts, including [SaucerSwap](https://www.saucerswap.finance/)'s DeFi contracts on the Hedera network:

* &#x20;SaucerSwap's HashScan verified DeFi smart contract:
  * `0x00000000000000000000000000000000002e7a5d`&#x20;
* The HashScan link to the verified smart contract can be found [here](https://hashscan.io/mainnet/contract/0.0.3045981?pf=1\&kf=0.0.1456986).
{% endhint %}

***

## API Endpoint Overview

The API endpoint and parameters used for all operations described in this guide is:

```bash
POST /api/v1/contracts/call
```

### Key Parameters

* `estimate` (boolean): Determines the operation type.
  * `true`: Performs gas estimation.
  * `false`: Executes a query or simulation.
* `block` (string): Specifies the block for the operation (e.g., "latest" or a specific block number).
* `data` (string): Encoded function call data in hexadecimal format following the ABI specifications.
* `from` (string, optional): Address initiating the call. Required for simulations involving state changes.
* `to` (string): Target smart contract address (SaucerSwap's DeFi contract for this guide).
* `value` (number, optional): Amount of tinybars to send with the transaction. Relevant for simulations involving value transfers.

For detailed specifications, refer to the [Swagger documentation](https://testnet.mirrornode.hedera.com/api/v1/docs/#/contracts/contractCall).

***

## Prerequisites

Before proceeding, ensure you have:

* Basic knowledge of EVM smart contracts:
  * Understanding of [ABI (Application Binary Interface)](compiling-smart-contracts.md#smart-contract-application-binary-interface-abi) and function encoding.
* Essential tools installed and configured:
  * cURL for making HTTP requests.
  * &#x20;Ethers.js (v6 or later or equivalent libraries) for interacting with the EVM-compatible networks.
* Function data encoding:
  * Familiarity with encoding function data into the required EVM-compatible hexadecimal format.

***

## Gas Estimation

Estimating gas usage helps determine the cost required to execute a smart contract function without actually performing the transaction.

### **Example Request**

Here's how to estimate gas usage using the `/api/v1/contracts/call` endpoint:

{% code overflow="wrap" %}
```bash
curl -X POST https://mainnet.mirrornode.hedera.com/api/v1/contracts/call \
-H "Content-Type: application/json" \
-d '{
    "block": "latest",
    "data": "0x1f00ca74000000000000000000000000000000000000000000000000000000003b9aca00000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000163b5a00000000000000000000000000000000000000000000000000000000000b2ad5",
    "estimate": true,
    "to": "0x00000000000000000000000000000000002e7a5d"
}'
```
{% endcode %}

### **Expected Response**&#x20;

The API returns an estimated gas value in hexadecimal format:

```json
{
  "result": "0x0000000000007f0d"
}
```

### **Decoding the Result:**

To interpret the hexadecimal gas estimate, follow these steps:

1. **Extract the `result`**: the field from the API response containing the gas estimate in hexadecimal format.
2.  **Convert Hexadecimal to BigInt**:

    Use JavaScript's `BigInt` to convert the hexadecimal string to a numerical value.

    ```javascript
    // Assuming you have the result from the API response
    const hexString = "0x0000000000007f0d";
    const gasEstimate = BigInt(hexString);
    console.log("Gas Estimate:", gasEstimate.toString());
    ```

    **Output:**

    ```bash
    Gas Estimate: 32525 
    ```

{% hint style="info" %}
The gas estimate result represents the value in tinybars.
{% endhint %}

**Understanding the Logic**

* **Hexadecimal Representation:** Smart contracts and blockchain APIs often use hexadecimal strings to represent numerical values, ensuring precise and compact data transmission.
* **Conversion to BigInt:** JavaScript's `BigInt` is used to handle large integers that exceed the safe integer limit of the standard `Number` type, ensuring accuracy in calculations.
* **Interpretation:** The numerical value (`32525` in this case) represents the estimated gas required to execute the specified smart contract function.

***

## Contract Queries

Contract queries allow developers to retrieve data from smart contracts without altering the blockchain state. This is particularly useful for reading data such as token balances, contract states, and more.

### **Example Request**

Retrieve the token balance using a contract’s view function:

{% code overflow="wrap" %}
```bash
curl -X POST https://mainnet.mirrornode.hedera.com/api/v1/contracts/call \
-H "Content-Type: application/json" \
-d '{
    "block": "latest",
    "data": "0x1f00ca74000000000000000000000000000000000000000000000000000000003b9aca00000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000163b5a00000000000000000000000000000000000000000000000000000000000b2ad5",
    "to": "0x00000000000000000000000000000000002e7a5d"
}'
```
{% endcode %}

### **Expected Response**

The API returns the result of the contract’s view function in hexadecimal format:

{% code overflow="wrap" %}
```json
{
  "result": "0x000000000000000000000000000000000000000000000000000000000000002000000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000efd80e2d6000000000000000000000000000000000000000000000000000000003b9aca00"
}
```
{% endcode %}

### Decoding the Result

To interpret the hexadecimal result (e.g., token balance), follow these steps:

1.  **Extract the** `result`**:**

    The `result` field contains the data returned by the smart contract function.
2.  **Convert Hexadecimal to BigInt:**



    {% code overflow="wrap" %}
    ```javascript
    const hexString = "0x000000000000000000000000000000000000000000000000000000000000002000000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000efd80e2d6000000000000000000000000000000000000000000000000000000003b9aca00";
    const balance = BigInt(hexString);
    console.log("Token Balance:", balance.toString());
    ```
    {% endcode %}

    **Output:**

    ```bash
    Token Balance: 32 # value represented in decimal
    ```

**Understanding the Logic**

* **Hexadecimal Representation:** The smart contract's `balanceOf` function returns the token balance in hexadecimal format.
* **Conversion to BigInt:** Using `BigInt` ensures accurate representation of potentially large token balances.
* **Interpretation:** The numerical value (`32` in this case) represents the token balance of the specified address

***

## EVM Transaction Simulations

Simulate EVM transactions (non-view functions) that involve state changes to test contract interactions without committing them. This can be useful for testing token transfers, approvals, and other state-altering functions without changing the blockchain state.&#x20;

### **Example Request**

This example simulates a token transfer by testing contract interactions without altering the state.

{% code overflow="wrap" %}
```bash
curl -X POST https://mainnet.mirrornode.hedera.com/api/v1/contracts/call \
-H "Content-Type: application/json" \
-d '{
    "block": "latest",
    "data": "0x",
    "estimate": false,
    "from": "0x00000000000000000000000000000000000004e2",
    "to": "0x00000000000000000000000000000000000004e4",
    "value": 1000
}'
```
{% endcode %}

### **Expected Response**

The empty result indicates that the simulation ran successfully without errors:

```json
{
  "result": "0x"
}
```

***

## Decoding the Results with Ethers.js

To decode the data returned by the Mirror Node, you need the [ABI](../../support-and-community/glossary.md#application-binary-interface-abi) of the smart contract you are interacting with. The ABI defines the structure of inputs and outputs for the contract's functions.

### Step 1: Install Ethers.js

If you haven't already, install Ethers.js using npm:

```bash
npm install ethers
```

### Step 2: Decode the Hexadecimal Result

Below is a detailed explanation of how to extract and interpret the `result` from the API response.

**Example Scenario**

You have made an API request to retrieve a token balance, and received the following response:

```json
{
  "result": "0x0000000000007f0d"
}
```

You want to convert this hexadecimal result to a human-readable token balance.

**JavaScript Code Example**

```javascript
const { ethers } = require('ethers');

// Example API response
const response = {
  data: {
    result: "0x0000000000007f0d" // Replace with actual API result
  }
};

// Step 1: Extract the hex string from the API response
const hexString = response.data.result;

// Step 2: Convert the hexadecimal string to a BigInt
const tokenBalance = BigInt(hexString);

// Step 3: Display the token balance
console.log("Token Balance:", tokenBalance.toString());
```

**Output:**

```yaml
Token Balance: 32525 # value represented in decimals
```

**Understanding the Logic**

1.  **Extracting the** `result`**:**

    ```javascript
    const hexString = response.data.result;
    ```

    * **Purpose:** Assign the `result` from the API response to the variable `hexString`.
    * **Content:** The `hexString` contains the ABI-encoded data returned by the smart contract function.
2.  **Converting Hexadecimal to BigInt:**

    ```javascript
    const tokenBalance = BigInt(hexString);
    ```

    * **Purpose:** Convert the hexadecimal string to a `BigInt` for numerical operations.
    * **Explanation:**
    * `BigInt`**:** A JavaScript data type that can represent integers with arbitrary precision, suitable for handling large numbers often used in blockchain applications.
    * **Conversion:** The `BigInt` constructor automatically parses the hexadecimal string (prefixed with `0x`) and converts it to its numerical equivalent.
3.  **Displaying the Token Balance:**

    ```javascript
    console.log("Token Balance:", tokenBalance.toString());
    ```

    * **Purpose:** Output the numerical value of the token balance to the console.
    * **Explanation:**
      * **`.toString()`:** Converts the `BigInt` to a string for readable output.

**Practical Example with Ethers.js for Complex Decoding**

For more complex return types (e.g., multiple values, arrays), Ethers.js can be used to decode the `result` based on the contract's ABI.

{% code overflow="wrap" %}
```javascript
const { ethers } = require('ethers');

// Example API response
const response = {
  data: {
    result: "0x000000000000002000000000000000000000000000000000000000000000000020000000efd80e2d6000000000000000000000000000000000000000000000000000000003b9aca00"
  }
};

// Step 1: Extract the hex string from the API response
const hexString = response.data.result;

// Step 2: Define the ABI for the function you want to decode
const abi = [
  'function getAmountsIn(uint amountOut, address[] calldata path) external view returns (uint[] memory amounts)'
];

// Step 3: Create an interface using the ABI
const abiInterface = new ethers.Interface(abi);

// Step 4: Decode the result using decodeFunctionResult
const decodedResult = abiInterface.decodeFunctionResult('getAmountsIn', hexString);

// Step 5: Access the decoded data
console.log("Decoded Amounts:", decodedResult[0].toString());
```
{% endcode %}

**Output:**

```yaml
Decoded Amounts: 32525 # value represented in tinybars
```

**Explanation**

1.  **Define the ABI:**

    ```javascript
    const abi = [
      'function getAmountsIn(uint amountOut, address[] calldata path) external view returns (uint[] memory amounts)'
    ];
    ```

    * The ABI specifies the `getAmountsIn` function which returns an array of `uint256` values.
2.  **Create an Interface:**

    ```javascript
    const abiInterface = new ethers.Interface(abi);
    ```

    * `ethers.Interface` uses the ABI to understand how to decode the data.
3.  **Decode the Result:**

    ```javascript
    const decodedResult = abiInterface.decodeFunctionResult('getAmountsIn', hexString);
    ```

    * `decodeFunctionResult` interprets the `result` based on the function's return type.
4.  **Access the Decoded Data:**

    ```javascript
    console.log("Decoded Amounts:", decodedResult[0].toString());
    ```

    * The decoded result is accessed as `decodedResult[0]` and converted to a string for readability.

***

## Reference

* [HIP-584 Proposal](https://hips.hedera.com/hip/hip-584)
* [REST API Documentation](https://docs.hedera.com/hedera/sdks-and-apis/rest-api)
* [Swagger Documentation](https://testnet.mirrornode.hedera.com/api/v1/docs/#/contracts/contractCall)
* [Ethers.js Documentation](https://docs.ethers.org/v5/)
