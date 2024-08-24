# Hedera Custodians Library

## Introduction

The Hedera Custodians Library is a TypeScript utility designed to simplify significantly managing a custodial wallet and its associated accounts for integration with the Hedera network. It gives developers a strong base for managing custodial wallets within their TypeScript applications, allowing them to focus on core logic.

#### Key Features

* Simplifies integration with the Hedera network
* Abstracts away complexities of custodial wallet management
* Provides a unified interface for different custodial services
* Enhances security and compliance in digital asset management

By leveraging the Hedera Custodians Library, developers can efficiently implement secure and scalable custodial wallet solutions in their Hedera-based applications.

## Custodial Wallet Management

Custodial wallet management is the practice of a third party being trusted with storage and security concerning the private keys that are associated with the cryptocurrency. Within the Hedera ecosystem, custodial wallet management is essential for the following reasons:

* Security: Custodial services have robust security and safety mechanisms to maintain digital assets.&#x20;
* Institutional Adoption: Many institutional investors require custodial solutions to comply with regulatory requirements and internal risk management policies.&#x20;
* Simplified User Experience: Custodial services may abstract the complexities of key management, making the user's interaction with the Hedera network much easier.&#x20;
* Integration with Traditional Finance: This often bridges cryptocurrency and traditional finance, furthering adoption.&#x20;
* Multi-signature Support: It can be done in various ways through custodial implementations with multi-signature wallets, which contribute to higher security and make more complex governance structures possible.&#x20;
* Regulatory Compliance: Custodial services typically have some in-built compliance features to assist users in conforming to KYC/AML.&#x20;

The Hedera Custodian Library simplifies the integration of custodial wallet services into applications built on the Hedera network, thus enabling developers to access the above features without integrating complex custodial logic. Currently, the library supports two leading custodial services:

### Fireblocks

[Fireblocks](https://www.fireblocks.com/) is a digital asset storage, transfer, and settlement platform specifically built for financial institutions and other businesses transacting in cryptocurrencies and digital assets. In the context of the Hedera ecosystem:

* Secure storage: HBAR is stored securely on Fireblocks alongside Hedera tokens, using a combination of MPC (Multi-Party Computation) technology and hardware isolation.
* Securing transactions: It offers a policy engine that enables the setup of custom approval flows for every transaction conducted on the Hedera network.&#x20;
* Integration: Fireblocks can be integrated into the Hedera network, allowing seamless transactions with Hedera-based assets and their custodial management.&#x20;
* API access: The Hedera Custodian Library has full access to the API, which it uses for wallet automation and signing transactions.

### DFNS

[DFNS](https://www.dfns.co/) (Digital Financial Network & Security) is a custodial wallet infrastructure provider that offers MPC-based key management and transaction signing services. In the Hedera ecosystem:

* Key Management: DFNS provides secure key generation and management for Hedera accounts without exposing private keys.
* Programmable Authorization: It offers customizable authorization policies for Hedera transactions, enabling complex approval flows.
* Multi-Tenancy: DFNS supports multi-tenant architectures, allowing businesses to manage multiple Hedera accounts for different users or purposes.
* API-First Approach: The service provides RESTful APIs that the Hedera Custodian Library uses to interact with Hedera accounts and sign transactions.

Fireblocks and DFNS enable secure, scalable, and compliant management of Hedera accounts and assets. By supporting these services, the Hedera Custodian Library allows developers to choose the custodial solution that best fits their needs while providing a unified interface for interacting with custodial wallets in the Hedera ecosystem.

## API Reference

<details>

<summary><strong>CustodialWalletService</strong></summary>

The `CustodialWalletService` class is the main entry point for interacting with custodial wallets.

### Constructor

```typescript
constructor(config: FireblocksConfig | DFNSConfig)
```

Creates a new instance of the CustodialWalletService with the specified configuration.

### Methods

```typescript
async signTransaction(request: SignatureRequest): Promise<Uint8Array>
```

Signs a transaction using the configured custodial service.

* Parameters:
  * `request`: A `SignatureRequest` object containing the transaction to be signed.
* Returns: A `Promise` that resolves to a `Uint8Array` containing the signature.

</details>

<details>

<summary><strong>FireblocksConfig</strong></summary>

The `FireblocksConfig` class represents the configuration for the Fireblocks custodial service.

### Constructor

```typescript
constructor(
  apiKey: string,
  apiSecretKey: string,
  baseUrl: string,
  vaultAccountId: string,
  assetId: string
)
```

Creates a new FireblocksConfig instance.

### Properties

* `apiKey`: The API key for Fireblocks.
* `apiSecretKey`: The API secret key for Fireblocks.
* `baseUrl`: The base URL for the Fireblocks API.
* `vaultAccountId`: The Fireblocks vault account ID.
* `assetId`: The asset ID for the Hedera token in Fireblocks.

</details>

<details>

<summary><strong>DFNSConfig</strong></summary>

The `DFNSConfig` class represents the configuration for the DFNS custodial service.

### Constructor

```typescript
constructor(
  serviceAccountAuthorizationToken: string,
  serviceAccountCredentialId: string,
  serviceAccountPrivateKey: string,
  appOrigin: string,
  appId: string,
  walletId: string
)
```

Creates a new DFNSConfig instance.

### Properties

* `serviceAccountAuthorizationToken`: The authorization token for the DFNS service account.
* `serviceAccountCredentialId`: The credential ID for the DFNS service account.
* `serviceAccountPrivateKey`: The private key for the DFNS service account.
* `appOrigin`: The origin URL of the DFNS app.
* `appId`: The ID of the DFNS app.
* `walletId`: The ID of the DFNS wallet.

</details>

<details>

<summary><strong>SignatureRequest</strong></summary>

The `SignatureRequest` class represents a request to sign a transaction.

### Constructor

```typescript
constructor(transactionBytes: Uint8Array)
```

Creates a new SignatureRequest instance.

### Properties

* `transactionBytes`: A `Uint8Array` containing the transaction bytes to be signed.

</details>
