# Supported ERC Token Standards

Interact with Hedera Token Service fungible and non-fungible tokens in your Solidity contract using ERC-20 and ERC-721 standards.

## ERC-20 (Fungible)

### Supported

**name**

<mark style="color:purple;">`function name() public view returns (string)`</mark>

Returns the name of the token.

**symbol**

<mark style="color:purple;">`function symbol() public view returns (string)`</mark>

Returns the symbol of the token.

**decimals**

<mark style="color:purple;background-color:purple;">`function decimals() public view returns (uint8)`</mark>

Returns the number of decimals the token uses.

**totalSupply**

<mark style="color:purple;">`function totalSupply() external view returns (uint256)`</mark>

Returns the total supply of the token.

**balanceOf**

<mark style="color:purple;">`function balanceOf(address account) external view returns (uint256)`</mark>

Returns of the balance of the token in the specified account. The <mark style="color:purple;">`account`</mark> is the Hedera account ID <mark style="color:purple;">`0.0.x`</mark> in Solidity address format or the evm address of a contract that has been created via the `CREATE2` operation.

**transfer**

<mark style="color:purple;">`function transfer(address recipient, uint256 amount) external returns (bool)`</mark>

Transfer tokens from your account to a recipient account. The <mark style="color:purple;">`recipient`</mark> is the Hedera account ID <mark style="color:purple;">`0.0.x`</mark> in Solidity format or the evm address of a contract that has been created via `CREATE2` operation.

### Not Supported

The following ERC-20 operations are not currently supported.

* <mark style="color:purple;">`allowance`</mark>
* <mark style="color:purple;">`approve`</mark>
* <mark style="color:purple;">`transferFrom`</mark>

## ERC-721 (Non-Fungible)

### Supported

The following ERC-721 operations will be supported. Standard ERC-721 Events will be emitted as appropriate.

#### From interface ERC721

**balanceOf**

<mark style="color:purple;">`function balanceOf(address _owner) external view returns (uint256)`</mark>

Returns balance of the HTS non fungible token from the account owner. The <mark style="color:purple;">`_owner`</mark> is the Hedera account ID <mark style="color:purple;">`0.0.x`</mark> in Solidity format or the evm address of a contract that has been created via the `CREATE2` operation.

**ownerOf**

<mark style="color:purple;">`function ownerOf(uint256 _tokenId) external view returns (address)`</mark>

Returns the account ID of the specified HTS token owner. The <mark style="color:purple;">`_tokenId`</mark> is the Hedera token ID <mark style="color:purple;">`0.0.x`</mark> in Solidity format.

#### From interface ERC721Metadata

**name**

<mark style="color:purple;">`function name() external view returns (string _name)`</mark>

Returns the name of the HTS non fungible token.

**symbol**

<mark style="color:purple;">`function symbol() external view returns (string _symbol)`</mark>

Returns the symbol of the HTS non fungible token.

**tokenURI**

<mark style="color:purple;">`function tokenURI(uint256 _tokenId) external view returns (string)`</mark>

Returns the token metadata of the HTS non fungible token. This corresponds to the NFT meta data field when minting an NFT using HTS.

#### From interface ERC721Enumerable

**totalSupply**

<mark style="color:purple;">`function totalSupply() external view returns (uint256)`</mark>

Returns the total supply of the HTS non-fungible token.

### Not Supported

The following ERC-721 operations are currently not supported.

#### From interface ERC721

* <mark style="color:purple;background-color:purple;">`safeTransferFrom`</mark>

#### All semantics of interface ERC721TokenReceiver.

* Existing Hedera token association rules will take the place of such checks.

#### From interface ERC721Enumerable

* <mark style="color:purple;">`tokenByIndex`</mark>
* <mark style="color:purple;">`tokenOfOwnerByIndex`</mark>

#### From interface ERC721

* <mark style="color:purple;">`approve`</mark>
* <mark style="color:purple;">`setApprovalForAll`</mark>
* <mark style="color:purple;">`getApproved`</mark>
* <mark style="color:purple;">`isApprovedForAll`</mark>
* <mark style="color:purple;">`transferFrom`</mark>

## Examples

* [ERC20Contract.sol](https://github.com/hashgraph/hedera-services/blob/master/test-clients/src/main/resource/contract/contracts/ERC20Contract/ERC20Contract.sol)
* [ERC721Contract.sol](https://github.com/hashgraph/hedera-services/blob/master/test-clients/src/main/resource/contract/contracts/ERC721Contract/ERC721Contract.sol)

## References

* [HIP-218](https://hips.hedera.com/hip/hip-218)
* [EIP-20](https://eips.ethereum.org/EIPS/eip-20)
* [EIP-721](https://eips.ethereum.org/EIPS/eip-721)
