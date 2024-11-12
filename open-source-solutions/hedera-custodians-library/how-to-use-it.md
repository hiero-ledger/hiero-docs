# How to use it

## Usage Guide

To install the hedera-custodians-library from NPM, run the following command in your terminal:

```sh
npm install @hashgraph/hedera-custodians-integration
```

This command will install the hedera-custodians-library along with all its necessary dependencies. Ensure you are in your project’s root directory or the appropriate subdirectory where you wish to add the library.

### Creating a Service

To create a custodial wallet service:

1. Choose the appropriate configuration (`FireblocksConfig` or `DFNSConfig`).
2. Instantiate the configuration with the required parameters.
3. Create a `CustodialWalletService` instance using the configuration.

```typescript
import { CustodialWalletService, FireblocksConfig } from '@hashgraph/hedera-custodians-integration';

const config = new FireblocksConfig(
  FIREBLOCKS_API_KEY,
  FIREBLOCKS_API_SECRET_KEY,
  FIREBLOCKS_BASE_URL,
  FIREBLOCKS_VAULT_ACCOUNT_ID,
  FIREBLOCKS_ASSET_ID
);

const service = new CustodialWalletService(config);
```

### Signing Transactions

To sign a transaction:

1. Create a `SignatureRequest` with the transaction bytes.
2. Use the signTransaction method of the `CustodialWalletService`.

```typescript
import { SignatureRequest } from '@hashgraph/hedera-custodians-integration';

const transactionBytes = new Uint8Array([/* your transaction bytes */]);
const request = new SignatureRequest(transactionBytes);

const signature = await service.signTransaction(request);
```

### Managing Multiple Wallets

To manage multiple wallets:

1. Create separate configurations for each wallet.
2. Instantiate a `CustodialWalletService` for each configuration.

```typescript
const fireblocksConfig = new FireblocksConfig(/* Fireblocks parameters */);
const dfnsConfig = new DFNSConfig(/* DFNS parameters */);

const fireblocksService = new CustodialWalletService(fireblocksConfig);
const dfnsService = new CustodialWalletService(dfnsConfig);
```

## Additional Resources

➡ [**Hedera Custodians Library Repository**](https://github.com/hashgraph/hedera-custodians-library)
