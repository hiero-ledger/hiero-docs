# Hedera Service Solidity Libraries

## Hedera Token Service

Hedera Token Service integration allows you to write token transactions natively in Solidity smart contracts. There are a few **Solidity source files** available to developers.

* [HederaTokenService.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/HederaTokenService.sol)
* [HederaResponseCodes.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/HederaResponseCodes.sol)
* [IHederaTokenService.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/IHederaTokenService.sol)
* [ExpiryHelper.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/ExpiryHelper.sol)
* [FeeHelper.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/FeeHelper.sol)
* [KeyHelper.sol](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/KeyHelper.sol)

The Hedera Token Service Solidity file provides the transactions to interact with tokens created on Hedera. The Hedera Response Codes contract provides the response codes associated with network errors. The IHedera Token Service is a supporting library for the Hedera Token Service Solidity file. You can grab these libraries [here](https://github.com/hashgraph/hedera-smart-contracts/tree/main/contracts/system-contracts/hedera-token-service) to add to your project and import them to your Solidity contract. Please see the example file below.

{% code title="ContractExample.sol" %}
```solidity
// SPDX-License-Identifier: GPL-3.0

pragma solidity >=0.7.0 <0.9.0;

import "./HederaTokenService.sol";
import "./HederaResponseCodes.sol";

contract contractExample is HederaTokenService {
...
int response = HederaTokenService.transferToken(tokenAddress, msg.sender, address(this), amount);
...
}
```
{% endcode %}

{% hint style="info" %}
**Note:** Although the [IHederaTokenService.sol ](https://github.com/hashgraph/hedera-smart-contracts/tree/main/contracts/system-contracts/hedera-token-service/IHederaTokenService.sol)file is not imported in the contract, you will need it in your project directory for the supporting libraries to reference.
{% endhint %}

## HederaTokenService.sol API Docs

### Create Tokens

{% hint style="info" %}
[HIP-358](https://hips.hedera.com/hip/hip-358): Token create precompile is live on previewnet and testnet. The [TokenCreateContract](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/examples/token-create/TokenCreateContract.sol) example contains four examples of how to create a token using the token create solidity libraries.
{% endhint %}

### <mark style="color:purple;">`createFungibleToken(token, initialTotalSupply, decimals)`</mark>

A transaction that creates a fungible token. Returns the new token address.

| **Param**            | **Type**                               | **Description**                                                                                                                                                         |
| -------------------- | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `token`              | IHederaTokenService.HederaToken memory | The basic properties of the token being created. This includes the token name, symbol, treasury account, keys, expiry, etc.                                             |
| `initialTotalSupply` | uint                                   | Specifies the initial supply of tokens to be put in circulation. The initial supply is sent to the Treasury Account. The supply is in the lowest denomination possible. |
| `decimals`           | uint                                   | The number of decimal places a token is divisible by.                                                                                                                   |

### <mark style="color:purple;">`createFungibleTokenWithCustomFees(token, initialTotalSupply, decimals, fixedFees, fractionalFees)`</mark>

A transaction that creates a fungible token with custom fees. Returns the new token address.

| **Param**            | **Type**                               | **Description**                                                                                                                                                         |
| -------------------- | -------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `token`              | IHederaTokenService.HederaToken memory | The basic properties of the token being created. This includes the token name, symbol, treasury account, keys, expiry, etc.                                             |
| `initialTotalSupply` | uint                                   | Specifies the initial supply of tokens to be put in circulation. The initial supply is sent to the Treasury Account. The supply is in the lowest denomination possible. |
| `decimals`           | uint                                   | The number of decimal places a token is divisible by.                                                                                                                   |
| `fixedFees`          | IHederaTokenService.FixedFee\[]        | List of fixed fees to apply to the token.                                                                                                                               |
| `fractionalFees`     | IHederaTokenService.FractionalFee\[]   | List of fractional fees to apply to the token.                                                                                                                          |

### <mark style="color:purple;">`createNonFungibleToken(token)`</mark>

Creates a non fungible token. Returns the new token address.

| **Param** | **Type**                               | **Description**                                                                                                             |
| --------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `token`   | IHederaTokenService.HederaToken memory | The basic properties of the token being created. This includes the token name, symbol, treasury account, keys, expiry, etc. |

### <mark style="color:purple;">`createNonFungibleTokenWithCustomFees(token, fixedFees, fractionalFees)`</mark>

Creates a non fungible token with custom fees. Returns the new token address.

| **Param**        | **Type**                               | **Description**                                                                                                             |
| ---------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `token`          | IHederaTokenService.HederaToken memory | The basic properties of the token being created. This includes the token name, symbol, treasury account, keys, expiry, etc. |
| `fixedFees`      | IHederaTokenService.FixedFee\[]        | List of fixed fees to apply to the token.                                                                                   |
| `fractionalFees` | IHederaTokenService.FractionalFee\[]   | List of fractional fees to apply to the token.                                                                              |

### Transfer Tokens

### <mark style="color:purple;">`cryptoTransfer(tokenTransfers)`</mark>

A transaction that transfers the provided list of tokens.

ABI Version: 2

| **Param**        | **Type**                                                                                                                                                     | **Description**             |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------- |
| `tokenTransfers` | [IHederaTokenService.TokenTransferList\[\] ](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/IHederaTokenService.sol)memory | The list of token transfers |

### <mark style="color:purple;">`transferToken(token, sender, receiver, amount)`</mark>

Transfers tokens where the calling account/contract is implicitly the first entry in the token transfer list, where the amount is the value needed to zero balance the transfers. The account address sending the token is required to sign the transaction.

ABI Version: 1

| **Param**  | **Type**                                                               | **Description**                                                                                                                     |
| ---------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `token`    | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address) | The token ID to transfer to/from. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address.         |
| `sender`   | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address) | The address sending the token. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address.            |
| `receiver` | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address) | The address of the receiver of the token. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address. |
| `amount`   | int64                                                                  | Non-negative value of the token to send. A negative value will result in a failure.                                                 |

### <mark style="color:purple;">`transferTokens(token, accountIds, amount)`</mark>

Initiates a fungible token transfer. This transaction accepts zero unit token transfer operations for fungible tokens ([HIP-564](https://hips.hedera.com/hip/hip-564)). Not applicable to non-fungible tokens.

ABI Version: 1

| **Param**    | **Type**                                                                                                   | **Description**                                                                                                                     |
| ------------ | ---------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `token`      | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address)                                     | The token ID to transfer. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address.                 |
| `accountIds` | [address](https://docs.soliditylang.org/en/v0.6.2/types.html?highlight=address%20%5B%5D#address)\[] memory | Hedera accounts to do a transfer to/from. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address. |
| `amounts`    | int64\[] memory                                                                                            | Non-negative value to send. A negative value will result in a failure.                                                              |

### <mark style="color:purple;">`transferNFT(token, sender, receiver, serialNum)`</mark>

Transfers tokens where the calling account/contract is implicitly the first entry in the token transfer list, where the amount is the value needed to zero balance the transfers. The address sending the token is required to sign the transaction.

ABI Version: 1

| **Param**   | **Type**                                                               | **Description**                                                                                                                     |
| ----------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `token`     | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address) | The token to transfer to/from.                                                                                                      |
| `sender`    | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address) | The address sending the token. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address.            |
| `receiver`  | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address) | The address of the receiver of the token. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address. |
| `serialNum` | int64                                                                  | The serial number of the NFT to transfer.                                                                                           |

### <mark style="color:purple;">`transferNFTs(token, sender, receiver, serialNumber)`</mark>

Initiates a non-fungible token transfer

ABI Version: 1

| **Param**      | **Type**                                                                         | **Description**                                                                                                                     |
| -------------- | -------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `token`        | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address)           | The ID of the token to transfer. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address.          |
| `sender`       | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address)\[] memory | The address sending the token. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address.            |
| `receiver`     | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address)\[] memory | The address of the receiver of the token. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address. |
| `serialNumber` | int64                                                                            | The serial number of the NFT is sent by the same index of the sender.                                                               |

### Mint Tokens

### <mark style="color:purple;">`mintToken(token, amount, metadata)`</mark>

Mints an amount of the token to the defined treasury account. This transaction accepts zero-amount token mint operations for fungible tokens ([HIP-564](https://hips.hedera.com/hip/hip-564)).

ABI Version: 2

| **Param**  | **Type**                                                               | **Description**                                                                                                                                                                                                                      |
| ---------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `token`    | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address) | The Hedera token to mint. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address.                                                                                                                  |
| `amount`   | <p><br>uint64</p>                                                      | <p>Applicable to FUNGIBLE TOKENS ONLY.<br><br>The amount to mint to the Treasury Account. Amount must be a positive non-zero number represented in the lowest denomination of the token. The new supply must be lower than 2^63.</p> |
| `metadata` | bytes\[] memory                                                        | <p>Applicable to NON-FUNGIBLE TOKENS ONLY.<br>Maximum allowed size of each metadata is 100 bytes</p>                                                                                                                                 |

### Burn Tokens

### <mark style="color:purple;">`burnToken(token, amount, serialNumbers)`</mark>

Burns an amount of the token from the defined treasury account<mark style="color:purple;">.</mark> This transaction accepts zero amount token burn operations for fungible tokens ([HIP-564](https://hips.hedera.com/hip/hip-564)).

ABI Version: 2

| **Param**       | **Type**                                                               | **Description**                                                                                                                                                                                                                                            |
| --------------- | ---------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `token`         | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address) | The token to burn. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address.                                                                                                                                               |
| `amount`        | uint64                                                                 | <p>Applicable to FUNGIBLE TOKENS ONLY.<br><br>The amount to burn from the Treasury Account. Amount must be a positive non-zero number, not bigger than the token balance of the treasury account (0; balance], represented in the lowest denomination.</p> |
| `serialNumbers` | int64\[]                                                               | <p>Applicable to NON-FUNGIBLE TOKENS ONLY.<br></p><p>The list of serial numbers to be burned.</p>                                                                                                                                                          |

### Associate Tokens

### <mark style="color:purple;">`associateToken(account, tokens)`</mark>

Associates the provided account with the provided tokens. Must be signed by the account that is associated with the token.

ABI Version: 2

| **Param** | **Type**                                                                                                   | **Description**                                                                                                                                                                                                                                                                                                                    |
| --------- | ---------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `account` | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address)                                     | The account to be associated with the provided tokens. The address is a mapping of `shard.realm.number` (0.0.x) into a 20 byte Solidity address.                                                                                                                                                                                   |
| `token`   | [address](https://docs.soliditylang.org/en/v0.6.2/types.html?highlight=address%20%5B%5D#address)\[] memory | <p>The list of tokens to be associated with the provided account.</p><p><br>In the case of non-fungible tokens, once an account is associated, it can hold any number of NFTs (serial numbers) of that token type.<br><br>The address is a mapping of <code>shard.realm.number</code> (0.0.x) into a 20 byte Solidity address.</p> |

### Dissociate Tokens

### <mark style="color:purple;">`dissociateToken(account, token)`</mark>

Dissociates the provided account with the provided token. Must be signed by the provided Account's key.

ABI Version: 2

| **Param** | **Type**                                                               | **Description**                                                                                                                                                                  |
| --------- | ---------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `account` | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address) | <p>The Hedera account to be dissociated from the provided token.<br><br>The address is a mapping of <code>shard.realm.number</code> (0.0.x) into a 20 byte Solidity address.</p> |
| `token`   | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address) | <p>The token to be dissociated from the provided account.<br><br>The address is a mapping of <code>shard.realm.number</code> (0.0.x) into a 20 byte Solidity address.</p>        |

### <mark style="color:purple;">`dissociateTokens(account, tokens)`</mark>

Dissociates the provided account with the provided token. Must be signed by the account the token is being dissociated from.

ABI Version: 2

| **Param** | **Type**                                                                                                   | **Description**                                                                                                                                                                    |
| --------- | ---------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `account` | [address](https://docs.soliditylang.org/en/v0.8.10/types.html#address)                                     | The account is to be dissociated from the provided tokens.                                                                                                                         |
| `tokens`  | [address](https://docs.soliditylang.org/en/v0.6.2/types.html?highlight=address%20%5B%5D#address)\[] memory | <p>The list of tokens to be dissociated from the provided account.<br><br>The address is a mapping of <code>shard.realm.number</code> (0.0.x) into a 20-byte Solidity address.</p> |

### <mark style="color:purple;">**`allowance(token, owner, spender)`**</mark>

Returns the amount the spender is still allowed to withdraw from the owner. Only applicable to fungible tokens.

| **Param** | **Type** | **Description**                                                   |
| --------- | -------- | ----------------------------------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format to check the allowance of. |
| `owner`   | address  | The owner account in Solidity format of the tokens to be spent.   |
| `spender` | address  | The spender account in Solidity format.                           |

### <mark style="color:purple;">`approve(token, spender, amount)`</mark>

Allows `spender` to withdraw from your account multiple times, up to the value amount. If this function is called again it overwrites the current allowance.

| **Param** | **Type** | **Description**                                                |
| --------- | -------- | -------------------------------------------------------------- |
| `token`   | address  | The Hedera token ID to approve in Solidity format.             |
| `spender` | address  | The spender account ID authorized to spend in Solidity format. |
| `amount`  | uint256  | The amount of tokens the spender is authorized to spend.       |

### <mark style="color:purple;">`approveNFT(token, approved, serialNumber)`</mark>

Allow or reaffirm the approved address to transfer an NFT the approved address does not own. Applicable to non-fungible tokens (NFTs).

| **Param**      | **Type** | **Description**                                                                                    |
| -------------- | -------- | -------------------------------------------------------------------------------------------------- |
| `token`        | address  | The Hedera NFT token ID in solidity format to approve.                                             |
| `approved`     | address  | The Hedera account ID to approve in Solidity format. To revoke approvals pass in the zero address. |
| `serialNumber` | uint256  | The NFT serial number to approve.                                                                  |

### <mark style="color:purple;">`getApproved(token, serialNumber)`</mark>

Get the approved Hedera accounts for a single NFT. Applicable to non-fungible tokens (NFTs).

| **Param**      | **Type** | **Description**                                     |
| -------------- | -------- | --------------------------------------------------- |
| `token`        | address  | The Hedera token ID for the NFT in Solidity format. |
| `serialNumber` | int64    | The NFT serial number.                              |

### <mark style="color:purple;">`isApprovedForAll(token, owner, operator)`</mark>

Returns whether or not the operator account is approved to spend on behalf of the owner.

| **Param**  | **Type** | **Description**                          |
| ---------- | -------- | ---------------------------------------- |
| `token`    | address  | The Hedera token ID in Solidity format.  |
| `owner`    | address  | The Hedera account ID in Solidity format |
| `operator` | address  | The Hedera account ID in Solidity format |

### <mark style="color:purple;">`setApprovalForAll(token, operator, approved)`</mark>

Enable or disable approval for a third party ("operator") to manage all of `msg.sender`'s assets.

| **Param**  | **Type** | **Description**                             |
| ---------- | -------- | ------------------------------------------- |
| `token`    | address  | The Hedera token ID in Solidity format.     |
| `operator` | address  | The Hedera account ID in Solidity format.   |
| `approved` | bool     | The Boolean data type in `true` or `false`. |

### <mark style="color:purple;">`isFrozen(token, account)`</mark>

Returns whether or not the account was frozen. The response will return true is the account is frozen. This means the token cannot be transferred from the account until the account is unfrozen.

| **Param** | **Type** | **Description**                           |
| --------- | -------- | ----------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format.   |
| `account` | address  | The Hedera account ID in Solidity format. |

### <mark style="color:purple;">`isKyc(token, account)`</mark>

Returns whether or not the token has been granted KYC.

| **Param** | **Type** | **Description**                           |
| --------- | -------- | ----------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format.   |
| `account` | address  | The Hedera account ID in Solidity format. |

### <mark style="color:purple;">`isToken(token)`</mark>

Returns whether or not the token exists on Hedera.

| **Param** | **Type** | **Description**                         |
| --------- | -------- | --------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format. |

### <mark style="color:purple;">`deleteToken(token)`</mark>

Deletes the specified token.

| **Param** | **Type** | **Description**                         |
| --------- | -------- | --------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format. |

### <mark style="color:purple;">`getTokenCustomFees(token)`</mark>

Returns the custom fees for the specified token.

| **Param** | **Type** | **Description**                         |
| --------- | -------- | --------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format. |

### <mark style="color:purple;">`getTokenDefaultFreezeStatus(token)`</mark>

Returns the token freeze status on the specified token.

| **Param** | **Type** | **Description**                         |
| --------- | -------- | --------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format. |

### <mark style="color:purple;">`getTokenDefaultKycStatus(token)`</mark>

Returns the KYC status on the specified token.

| **Param** | **Type** | **Description**                         |
| --------- | -------- | --------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format. |

### <mark style="color:purple;">`getTokenExpiryInfo(token)`</mark>

Returns the token expiration for the specified token.

| **Param** | **Type** | **Description**                                |
| --------- | -------- | ---------------------------------------------- |
| `token`   | address  | The ID of the Hedera token in Solidity format. |

### <mark style="color:purple;">`getTokenInfo(token)`</mark>

Retrieves general token entity information for the specified token.

| **Param** | **Type** | **Description**                                |
| --------- | -------- | ---------------------------------------------- |
| `token`   | address  | The ID of the Hedera token in Solidity format. |

### <mark style="color:purple;">`getFungibleTokenInfo(token)`</mark>

Retrieves fungible specific token property information for a fungible token.

| **Param** | **Type** | **Description**                                               |
| --------- | -------- | ------------------------------------------------------------- |
| `token`   | address  | The Hedera token ID of the fungible token in Solidity format. |

### <mark style="color:purple;">`getNonFungibleTokenInfo(token)`</mark>

Retrieves non-fungible specific token info for a given NFT.

| **Param** | **Type** | **Description**                         |
| --------- | -------- | --------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format. |

### <mark style="color:purple;">`getTokenKey(token, keyType)`</mark>

Returns the token key for the specified key type. A key type can be be the KYC key, pause key, freeze key, etc.

| **Param** | **Type** | **Description**                                                 |
| --------- | -------- | --------------------------------------------------------------- |
| `token`   | address  | The ID of the token in solidity format to return the key value. |
| `keyType` | uint     | The keyType of the desired key value.                           |

### <mark style="color:purple;">`getTokenType(token)`</mark>

Returns the token type (fungible or non-fungible) for the specified token.

| **Param** | **Type** | **Description**                         |
| --------- | -------- | --------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format. |

### <mark style="color:purple;">`freezeToken(token, account)`</mark>

Freezes the account from transacting the specified token.

| **Param** | **Type** | **Description**                           |
| --------- | -------- | ----------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format.   |
| `account` | address  | The Hedera account ID in Solidity format. |

### <mark style="color:purple;">`unfreezeToken(token, account)`</mark>

Unfreezes the account from transacting the specified token.

| **Param** | **Type** | **Description**                           |
| --------- | -------- | ----------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format.   |
| `account` | address  | The Hedera account ID in Solidity format. |

### <mark style="color:purple;">`grantTokenKyc(token, account)`</mark>

Grants KYC to the Hedera account for the specified token.

| **Param** | **Type** | **Description**                           |
| --------- | -------- | ----------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format.   |
| `account` | address  | The Hedera account ID in Solidity format. |

### <mark style="color:purple;">**`revokeTokenKyc(token, account)`**</mark>

Revokes KYC to the Hedera account for the specified token.

| **Param** | **Type** | **Description**                           |
| --------- | -------- | ----------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format.   |
| `account` | address  | The Hedera account ID in Solidity format. |

### <mark style="color:purple;">`pauseToken(token)`</mark>

Prevents a token from being transacted if set.

| **Param** | **Type** | **Description**                         |
| --------- | -------- | --------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format. |

### <mark style="color:purple;">`unpauseToken(token)`</mark>

Unpauses a token from a previously paused state.

| **Param** | **Type** | **Description**                         |
| --------- | -------- | --------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format. |

### <mark style="color:purple;">`wipeTokenAccount(token, account, amount)`</mark>

Wipes fungible tokens from the specified account. This transaction accepts zero amount token wipe operations for fungible tokens ([HIP-564](https://hips.hedera.com/hip/hip-564)).

| **Param** | **Type** | **Description**                           |
| --------- | -------- | ----------------------------------------- |
| `token`   | address  | The Hedera token ID in Solidity format.   |
| `account` | address  | The Hedera account ID in Solidity format. |
| `amount`  | uint32   | The amount of fungible tokens to wipe.    |

### <mark style="color:purple;">`wipeTokenAccountNFT(token, token, serialNumbers)`</mark>

Wipes a non-fungible token from the specified account.

| **Param**       | **Type**        | **Description**                                  |
| --------------- | --------------- | ------------------------------------------------ |
| `token`         | address         | The Hedera token ID in Solidity format.          |
| `account`       | address         | The Hedera account ID in Solidity format.        |
| `serialNumbers` | int64\[] memory | The NFT serial numbers to wipe from the account. |

### <mark style="color:purple;">`updateTokenInfo(token, tokenInfo)`</mark>

Updates the properties of a token including the name, symbol, treasury account, memo, etc.

| **Param**   | **Type**                                                                                                                                                 | **Description**                         |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
| `token`     | address                                                                                                                                                  | The Hedera token ID in Solidity format. |
| `tokenInfo` | [IHederaTokenService.HederaToken](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/IHederaTokenService.sol) memory | The token properties to update.         |

### <mark style="color:purple;">`updateTokenExpiryInfo(token, expiryInfo)`</mark>

Update the token expiration time.

| **Param**    | **Type**                                                                                                                                            | **Description**                         |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
| `token`      | address                                                                                                                                             | The Hedera token ID in Solidity format. |
| `expiryInfo` | [IHederaTokenService.Expiry](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/IHederaTokenService.sol) memory | The expiry properties of a token.       |

### <mark style="color:purple;">`updateTokenKeys(token, keys)`</mark>

Update the keys set on a token. The key type is defined in the key parameter.

| **Param** | **Type**                                                                                                                                                  | **Description**                         |
| --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
| `token`   | address                                                                                                                                                   | The Hedera token ID in Solidity format. |
| `keys`    | [IHederaTokenService.TokenKey\[\]](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/IHederaTokenService.sol) memory | The token key type.                     |

### <mark style="color:purple;">`updateTokenKeys(token, keys)`</mark>

Update the keys set on a token. The key type is defined in the key parameter.

| **Param** | **Type**                                                                                                                                                  | **Description**                         |
| --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
| `token`   | address                                                                                                                                                   | The Hedera token ID in Solidity format. |
| `keys`    | [IHederaTokenService.TokenKey\[\]](https://github.com/hashgraph/hedera-smart-contracts/blob/main/contracts/system-contracts/hedera-token-service/IHederaTokenService.sol) memory | The token key type.                     |

## Gas Cost

{% content-ref url="../../../networks/mainnet/fees/" %}
[fees](../../../networks/mainnet/fees/)
{% endcontent-ref %}

## Examples

{% content-ref url="../../../tutorials/smart-contracts/deploy-a-contract-using-the-hedera-token-service.md" %}
[deploy-a-contract-using-the-hedera-token-service.md](../../../tutorials/smart-contracts/deploy-a-contract-using-the-hedera-token-service.md)
{% endcontent-ref %}
