# ERC-20 (Fungible Tokens)

The [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) standard defines a set of functions and events that a token contract on the Ethereum blockchain should implement. ERC-20 tokens are fungible, meaning each token is identical and can be exchanged one-to-one.

{% hint style="info" %}
**Note**: `ERC-20` Token addresses refer to full Hedera Token Service (HTS) fungible token entities. These tokens can be fully managed by HTS API calls. Additionally, by utilizing [`IERC20`](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#IERC721) interfaces or system contract functions, these tokens can also be managed by smart contracts on Hedera.
{% endhint %}

## Supported Functions

#### **From** I**nterface `ERC-20`**&#x20;

<details>

<summary><strong>name</strong></summary>

```solidity
function name() public view returns (string)
```

Returns the name of the token.

</details>

<details>

<summary><strong>symbol</strong></summary>

```solidity
function symbol() public view returns (string)
```

Returns the symbol of the token.

</details>

<details>

<summary><strong>decimals</strong></summary>

```solidity
function decimals() public view returns (uint8)
```

Returns the number of decimals the token uses.

</details>

<details>

<summary><strong>totalSupply</strong></summary>

```solidity
function totalSupply() external view returns (uint256)
```

Returns the total supply of the token.

</details>

<details>

<summary><strong>balanceOf</strong></summary>

```solidity
function balanceOf(address account) external view returns (uint256)
```

Returns of the balance of the token in the specified account. The <mark style="color:purple;">`account`</mark> is the Hedera account ID <mark style="color:purple;">`0.0.x`</mark> in Solidity address format or the evm address of a contract that has been created via the `CREATE2` operation.

</details>

<details>

<summary><strong>allowance</strong></summary>

{% code overflow="wrap" %}
```solidity
function allowance(address owner, address spender) external view returns (uint256)
```
{% endcode %}

Returns the remaining number of tokens that `spender` will be allowed to spend on behalf of `owner` through `transferFrom`. This is zero by default. This value changes when `approve` or `transferFrom` are called. This works by loading the owner `FUNGIBLE_TOKEN_ALLOWANCES` from the accounts ledger and returning the allowance approved for `spender` The `owner` and `spender` address are the account IDs (0.0.num) in solidity format.

</details>

<details>

<summary><strong>transfer</strong></summary>

```solidity
function transfer(address recipient, uint256 amount) external returns (bool)
```

Transfer tokens from your account to a recipient account. The <mark style="color:purple;">`recipient`</mark> is the Hedera account ID <mark style="color:purple;">`0.0.x`</mark> in Solidity format or the EVM address of a contract that has been created via `CREATE2` operation.

</details>

<details>

<summary><strong>transferFrom</strong></summary>

{% code overflow="wrap" %}
```solidity
function transferFrom(address sender, address recipient, uint256 amount) external returns (bool)
```
{% endcode %}

Moves `amount` tokens from `from` to `to` using the allowance mechanism. `amount` is then deducted from the caller's allowance.

This works by creating a synthetic `CryptoTransferTransaction` with fungible token transfers with the `is_approval` property set to true.

</details>

<details>

<summary><strong>approve</strong></summary>

```solidity
function approve(address spender, uint256 amount) external returns (bool)
```

Sets `amount` as the allowance of `spender` over the caller's tokens.

This works by creating a synthetic `CryptoApproveAllowanceTransaction` with payer - the account that called the precompile (the message sender property of the message frame in the EVM).

Fires an approval event with the following signature when executed:\
event Approval(address indexed owner, address indexed spender, uint256 value);

</details>

***

### **Additional References**

* [HIP-376](https://hips.hedera.com/hip/hip-376)
* [HIP-218](https://hips.hedera.com/hip/hip-218)
* [EIP-20](https://eips.ethereum.org/EIPS/eip-20)
