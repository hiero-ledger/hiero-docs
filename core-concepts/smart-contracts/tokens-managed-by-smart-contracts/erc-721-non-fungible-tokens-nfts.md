# ERC-721 (Non-Fungible Token)

The [ERC-721](https://ethereum.org/en/developers/docs/standards/tokens/erc-721/) standard introduces a [non-fungible token (NFT)](../../../support-and-community/glossary.md#non-fungible-token-nft) in which each issued token is unique and distinct from others. This standard defines functions and events that enable the creation, ownership, and transfer of non-fungible assets.&#x20;

{% hint style="info" %}
**Note**:`ERC-721` Token addresses refer to full Hedera Token Service (HTS) fungible token entities. These tokens can be fully managed by HTS API calls. Additionally, by utilizing [`IERC721`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#IERC721) interfaces or system contract functions, these tokens can also be managed by smart contracts on Hedera.
{% endhint %}

## Supported Functions&#x20;

#### **From** I**nterface `ERC-721`**&#x20;

<details>

<summary><strong>ownerOf</strong></summary>

```solidity
function ownerOf(uint256 _tokenId) external view returns (address)
```

Returns the account ID of the specified HTS token owner. The `_tokenId` is the Hedera serial number of the NFT.

</details>

<details>

<summary><strong>approve</strong></summary>

```solidity
function approve(address _approved, uint256 _tokenId) external payable
```

Gives the spender permission to transfer a token (`_tokenId`) to another account from the owner. The approval is cleared when the token is transferred. The `_tokenId` is the Hedera serial number of the NFT.

This works by creating a synthetic `CryptoApproveAllowanceTransaction` with payer - the account that called the precompile (the message sender property of the message frame in the EVM).

If the `spender` address is 0, this creates a `CryptoDeleteAllowanceTransaction` instead and removes any allowances previously approved on the token.

Fires an approval event with the following signature when executed:

event Approval(address indexed owner, address indexed approved, uint256 indexed tokenId);

</details>

<details>

<summary><strong>setApprovalForAll</strong></summary>

<mark style="color:purple;">`function setApprovalForAll(address _operator, bool _approved) external`</mark>

Approve or remove an `operator` as an operator for the caller. Operators can call `transferFrom` for any token owned by the caller.

This works by creating a synthetic `CryptoApproveAllowanceTransaction` with payer - the account that called the precompile (the message sender property of the message frame in the EVM).

</details>

<details>

<summary><strong>getApproved</strong></summary>

<mark style="color:purple;">`function getApproved(uint256 _tokenId) external view returns (address)`</mark>

```solidity
```

Returns the account approved for the specified `_tokenId`. The `_tokenId` is the Hedera serial number of the NFT.

This works by loading the `SPENDER` property of the token from the NFTs ledger.

</details>

<details>

<summary><strong>isApprovedForAll</strong></summary>

<mark style="color:purple;">`function isApprovedForAll(address _owner, address _operator) external view returns (bool)`</mark>

```solidity
```

Returns if the `operator` is allowed to manage all of the assets of `owner`.

This works by loading the `APPROVE_FOR_ALL_NFTS_ALLOWANCES` property of the owner account and verifying if the list of approved for all accounts contains the account id of the `operator`.

</details>

<details>

<summary><strong>transferFrom</strong></summary>

{% code overflow="wrap" %}
```solidity
function transferFrom(address _from, address _to, uint256 _tokenId) external payable
```
{% endcode %}

Transfers a token (`_tokenId`) from a Hedera account (`from`) to another Hedera account (`to`) in Solidity format. The `_tokenId` is the Hedera serial number of the NFT.

This works by creating a synthetic `CryptoTransferTransaction` with nft token transfers with the `is_approval` property set to true.

</details>

#### **From Interface `ERC721Metadata`**&#x20;

<details>

<summary><strong>name</strong></summary>

```solidity
function name() external view returns (string _name)
```

Returns the name of the HTS non-fungible token.

</details>

<details>

<summary><strong>symbol</strong></summary>

```solidity
function symbol() external view returns (string _symbol)
```

Returns the symbol of the HTS non-fungible token.

</details>

<details>

<summary><strong>tokenURI</strong></summary>

```solidity
function tokenURI(uint256 _tokenId) external view returns (string)
```

Returns the token metadata of the HTS non-fungible token. This corresponds to the NFT metadata field when minting an NFT using HTS. The `_tokenId` is the Hedera serial number of the NFT.

</details>

#### **From Interface `ERC721Enumerable`**&#x20;

<details>

<summary><strong>totalSupply</strong></summary>

```solidity
function totalSupply() external view returns (uint256)
```

Returns the total supply of the HTS non-fungible token.

</details>

***

## Unsupported Functions

The following ERC-721 operations will not  be natively supported on Hedera and will return a failure if they're called. Advanced functionality is achievable only through custom implementations within smart contracts.&#x20;

#### **From interface `ERC-721`**

<details>

<summary><strong>safeTransferFrom</strong></summary>

{% code overflow="wrap" %}
```solidity
function safeTransferFrom(address token, address from, address to, uint256 tokenId)
```
{% endcode %}

</details>

#### **From interface `ERC721Enumerable`**

<details>

<summary><strong>tokenByIndex</strong></summary>

{% code overflow="wrap" %}
```solidity
function tokenByIndex(uint256 _index) external view returns (uint256)
```
{% endcode %}

</details>

<details>

<summary><strong>tokenOfOwnerByIndex</strong></summary>

{% code overflow="wrap" %}
```solidity
function tokenOfOwnerByIndex(address _owner, uint256 _index) external view returns (uint256)
```
{% endcode %}

</details>

#### **All semantics of Interface `ERC721TokenReceiver`**

* Existing Hedera token association rules will take the place of such checks.

***

### **Additional References**

* [HIP-376](https://hips.hedera.com/hip/hip-376)
* [HIP-218](https://hips.hedera.com/hip/hip-218)
* [EIP-721](https://eips.ethereum.org/EIPS/eip-721)
