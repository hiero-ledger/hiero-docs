# ERC-721: Non-Fungible Tokens (NFTs)

The [ERC-721](https://ethereum.org/en/developers/docs/standards/tokens/erc-721/) standard introduces a non-fungible token (NFT) in which each issued token is unique and distinct from others. This standard defines functions and events that enable the creation, ownership, and transfer of non-fungible assets.

ERC-721 defines several key functions, including:

## Supported

#### From interface ERC721

<details>

<summary><strong>balanceOf</strong></summary>

<mark style="color:purple;">`function balanceOf(address _owner) external view returns (uint256)`</mark>

Returns balance of the HTS non fungible token from the account owner. The <mark style="color:purple;">`_owner`</mark> is the Hedera account ID <mark style="color:purple;">`0.0.x`</mark> in Solidity format or the evm address of a contract that has been created via the `CREATE2` operation.

</details>

<details>

<summary><strong>ownerOf</strong></summary>

<mark style="color:purple;">`function ownerOf(uint256 _tokenId) external view returns (address)`</mark>

Returns the account ID of the specified HTS token owner. The `_tokenId` is the Hedera serial number of the NFT.

</details>

<details>

<summary><strong>approve</strong></summary>

<mark style="color:purple;">`function approve(address _approved, uint256 _tokenId) external payable`</mark>

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

Returns the account approved for the specified `_tokenId`. The `_tokenId` is the Hedera serial number of the NFT.

This works by loading the `SPENDER` property of the token from the NFTs ledger.

</details>

<details>

<summary><strong>isApprovedForAll</strong></summary>

<mark style="color:purple;">`function isApprovedForAll(address _owner, address _operator) external view returns (bool)`</mark>

Returns if the `operator` is allowed to manage all of the assets of `owner`.

This works by loading the `APPROVE_FOR_ALL_NFTS_ALLOWANCES` property of the owner account and verifying if the list of approved for all accounts contains the account id of the `operator`.

</details>

<details>

<summary><strong>transferFrom</strong></summary>

<mark style="color:purple;">`function transferFrom(address _from, address _to, uint256 _tokenId) external payable`</mark>

Transfers a token (`_tokenId`) from a Hedera account (`from`) to another Hedera account (`to`) in Solidity format. The `_tokenId` is the Hedera serial number of the NFT.

This works by creating a synthetic `CryptoTransferTransaction` with nft token transfers with the `is_approval` property set to true.

</details>

#### From interface ERC721Metadata

<details>

<summary><strong>name</strong></summary>

<mark style="color:purple;">`function name() external view returns (string _name)`</mark>

Returns the name of the HTS non-fungible token.

</details>

<details>

<summary><strong>symbol</strong></summary>

<mark style="color:purple;">`function symbol() external view returns (string _symbol)`</mark>

Returns the symbol of the HTS non-fungible token.

</details>

<details>

<summary><strong>tokenURI</strong></summary>

<mark style="color:purple;">`function tokenURI(uint256 _tokenId) external view returns (string)`</mark>

Returns the token metadata of the HTS non-fungible token. This corresponds to the NFT metadata field when minting an NFT using HTS. The `_tokenId` is the Hedera serial number of the NFT.

</details>

#### From interface ERC721Enumerable

<details>

<summary><strong>totalSupply</strong></summary>

<mark style="color:purple;">`function totalSupply() external view returns (uint256)`</mark>

Returns the total supply of the HTS non-fungible token.

</details>

***

## Not Supported

The following ERC-721 operations are currently not supported and will return a failure if called.

#### From interface ERC721

- [<mark style="color:purple;">`safeTransferFrom`</mark>](https://github.com/hashgraph/hedera-services/blob/ee7ec373b4afb30b686c1778936da8f9cec98500/test-clients/src/main/resource/contract/contracts/ERC721Contract/ERC721Contract.sol#L55-L57)

#### All semantics of interface ERC721TokenReceiver.

- Existing Hedera token association rules will take the place of such checks.

#### From interface ERC721Enumerable

- [<mark style="color:purple;">`tokenByIndex`</mark>](https://github.com/hashgraph/hedera-services/blob/ee7ec373b4afb30b686c1778936da8f9cec98500/test-clients/src/main/resource/contract/contracts/ERC721Contract/ERC721Contract.sol#L65-L67)
- [<mark style="color:purple;">`tokenOfOwnerByIndex`</mark>](https://github.com/hashgraph/hedera-services/blob/ee7ec373b4afb30b686c1778936da8f9cec98500/test-clients/src/main/resource/contract/contracts/ERC721Contract/ERC721Contract.sol#L69-L71)

***

#### **Additional References**

- [HIP-376](https://hips.hedera.com/hip/hip-376)
- [HIP-218](https://hips.hedera.com/hip/hip-218)
- [EIP-721](https://eips.ethereum.org/EIPS/eip-721)
