# Accounts

Accounts are the central starting point when interacting with the Hedera network and using Hedera Services. A Hedera account is an entity, a distinct object type, stored in the ledger, that holds tokens. Accounts can hold the native Hedera fungible token (HBAR), custom fungible, and custom non-fungible tokens (NFTs) created on the Hedera network.

The Hedera native token HBAR (ℏ) is a utility token primarily used to pay for transactions and query fees when interacting with the network. The HBAR symbol is represented as "ℏ." Applications may reference HBAR as the token denomination; however, the network returns information in tinybars (tℏ), a denomination of HBAR. 100,000,000 tℏ are equivalent to 1 ℏ. This includes things like transaction fees or accounts HBAR balances.

You interact with the network by submitting transactions that modify the ledger's state or submitting query requests that read data from the ledger. Most transactions and queries have a [transaction fee](../../networks/mainnet/fees/) that is charged in HBAR. Unlike custom tokens users create on the Hedera network, no token ID represents the native HBAR token.

<table data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><a href="account-creation.md"><strong>Account Creation</strong></a></td><td><a href="account-creation.md">account-creation.md</a></td></tr><tr><td><a href="auto-account-creation.md"><strong>Auto Account Creation</strong></a></td><td><a href="auto-account-creation.md">auto-account-creation.md</a></td></tr><tr><td><a href="account-properties.md"><strong>Account Properties</strong></a></td><td><a href="account-properties.md">account-properties.md</a></td></tr></tbody></table>

## FAQ

<details>

<summary>What is a Hedera account?</summary>

A Hedera account is a unique entity in the Hedera Network that can hold tokens. These can be Hedera's native fungible token (HBAR), custom fungible, or [non-fungible tokens (NFTs)](../../support-and-community/glossary.md#non-fungible-token-nft).

</details>

<details>

<summary>How are new accounts created on Hedera?</summary>

New accounts are created by submitting a transaction to the network and paying the transaction fee. You'll need access to an existing account with sufficient HBAR to cover this fee. If you don't have access to an existing account, you can use a supported wallet, visit the [Hedera Developer Portal](https://portal.hedera.com/), or use the "Auto Account Creation" feature for applications.

</details>

<details>

<summary>What is the 'Auto Account Creation' feature?</summary>

[Auto Account Creation](auto-account-creation.md) allows applications to generate free user accounts instantly, even without an internet connection, by creating an account alias.

</details>

<details>

<summary>What is a "hollow" account?</summary>

If an account is created with an [EVM address](auto-account-creation.md#evm-address) alias via [Auto Account Creation](auto-account-creation.md), it results in a "hollow" account. This account has an account number and alias but no account key. It can accept token transfers but cannot transfer tokens or modify account properties until the account key has been added, completing the account.

</details>
