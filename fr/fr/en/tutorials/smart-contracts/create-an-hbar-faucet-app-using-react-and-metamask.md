# Create an HBAR Faucet App Using React and MetaMask

Enabling the opportunity to connect to a decentralized application (dApp) with different wallets provides more control to the user. Recently, [HIP-583](https://hips.hedera.com/hip/hip-583) added the necessary network infrastructure to support Ethereum Virtual Machine (EVM) wallets on the Hedera network. This added functionality, combined with the auto-create flow described in [HIP-32](https://hips.hedera.com/hip/hip-32), enables developers to transfer native Hedera Token Service (HTS) tokens to EVM addresses that do not yet exist on the Hedera network. In this tutorial, we start out with a Hedera react app, connect our dApp to MetaMask, and finally transfer HBAR to the connected MetaMask account.

#### What we will do:

This tutorial will show you how to create a Hedera React app using TypeScript and Material UI. You'll install the MetaMask Chrome extension, add the Hedera Testnet network, connect your dApp to it, and even send some HBAR to your MetaMask wallet.

<details>

<summary>Project Repos</summary>

The complete TypeScript project can be found on GitHub [here](https://github.com/a-ridley/hbar-faucet-for-metamask).

The complete JavaScript project can be found on GitHub [here](https://github.com/a-ridley/hbar-faucet-for-metamask-JS).

</details>

***

## Prerequisites

- Create a Hedera Testnet account [here](../../getting-started/introduction.md).
- Install the [MetaMask Chrome extension](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn?hl=en).&#x20
- Basic understanding of [TypeScript](https://www.typescriptlang.org/) and [React](https://react.dev/).
- Set up your environment and create your client [here](../../getting-started/environment-set-up.md).&#x20

***

## Table of Contents

1. [Create React App](create-an-hbar-faucet-app-using-react-and-metamask.md#create-react-app)
2. [Configure MetaMask](create-an-hbar-faucet-app-using-react-and-metamask.md#configure-metamask-with-hedera-testnet-network)
3. [Connect MetaMask](create-an-hbar-faucet-app-using-react-and-metamask.md#connect-our-dapp-to-metamask-and-retrieve-wallet-address)
4. [Install Dependencies](create-an-hbar-faucet-app-using-react-and-metamask.md#install-dependencies)
5. [Create Client](create-an-hbar-faucet-app-using-react-and-metamask.md#create-your-client)
6. [Send HBAR to MetaMask](create-an-hbar-faucet-app-using-react-and-metamask.md#send-hbar-to-metamask-wallet)
7. [Summary](create-an-hbar-faucet-app-using-react-and-metamask.md#summary)
8. [Additional Resources](create-an-hbar-faucet-app-using-react-and-metamask.md#additional-resources)

***

## Create React App

Open your terminal and run the following command to create a React app that utilizes TypeScript and Material UI.

```bash
npx create-react-app <app-name> --template hedera-theme
```

This creates react-app theme that provides a navbar with a button, footer, react-router, and a global context for state management. We will use this template to kickstart our project.

Open the project in Visual Studio code or your IDE of choice. Your project directory structure will look like this:

```
├── node_modules
├── publicLA
├── src
├── .gitignore
├── package-lock.json
├── package.json
├── README.md
└── tsconfig.json
```

**If you have not already installed the MetaMask Chrome extension, please install it** [**here**](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn?hl=en)**.**

***

## Configure MetaMask with Hedera Testnet Network

In this step, we will add the code necessary to add the Hedera Testnet network to MetaMask so we can connect to it. Before we do that, let’s check out our project folder structure, which looks like this:

```
src
├── assets
├── components
├── App.tsx
├── AppRouter.tsx
├── Home.tsx
├── index.tsx
├── react-app-env.ts
```

Let’s continue and add a new folder inside src and name it services. Inside the services folder, add a file named `metamaskService.ts`. Your `src` folder structure will look like this:

```
src
├── assets
├── components
├── services
	├── metamaskServices.ts
├── App.tsx
├── AppRouter.tsx
├── Home.tsx
├── index.tsx
├── react-app-env.ts
```

We will store the code in the services folder, which will be used to support certain services required to run our application. We added the `metamaskService.ts file` to hold the code necessary for our MetaMask integration with our application. This distinction helps keep our code organized and clean.

It’s important to note that to add the Hedera test network to MetaMask, we must determine if MetaMask exists in our browser. We can achieve this by using the window, aka the [Browser Object Model](https://www.w3schools.com/js/js\_window.asp) (BOM), which represents the browser's window. This window object can access all global JavaScript objects, functions, and variables. If we are successful in accessing the Ethereum object off of the window object, then it is an indication that MetaMask is installed. If it is not successful, then we will handle that by throwing an error message to the console that reads, “MetaMask is not installed! Go install the extension!”

Let's begin!

In our `metamaskService.ts file`, create a function named `switchToHederaNetwork`.

```typescript
export const switchToHederaNetwork = async (ethereum: any) => {
  try {
    await ethereum.request({
      method: 'wallet_switchEthereumChain',
      params: [{ chainId: '0x128' }] // chainId must be in hexadecimal numbers
    });
  } catch (error: any) {
    if (error.code === 4902) {
      try {
        await ethereum.request({
          method: 'wallet_addEthereumChain',
          params: [
            {
              chainName: 'Hedera Testnet',
              chainId: '0x128',
              nativeCurrency: {
                name: 'HBAR',
                symbol: 'HBAR',
                decimals: 18
              },
              rpcUrls: ['https://testnet.hashio.io/api']
            },
          ],
        });
      } catch (addError) {
        console.error(addError);
      }
    }
    console.error(error);
  }
}
```

Let’s digest this function a little further. This function starts by submitting a request to change to the network to Hedera Testnet. If it fails to connect due to the Hedera Testnet network not being configured in MetaMask, then we will submit a request to add a new chain.\
\
In the request to add a new chain, the decimal value is set to 18 even though HBAR has 8 decimals. The reason for this is that MetaMask only supports chains that have 18 decimals. The RPC URL we use is [**https://testnet.hashio.io/api**](https://testnet.hashio.io/api), which comes from [Hashio](https://swirldslabs.com/hashio/), the SwirldsLabs-hosted version of the [JSON-RPC Relay](../../core-concepts/smart-contracts/deploying-smart-contracts/json-rpc-relay.md).

***

## Connect Our dApp to MetaMask and Retrieve Wallet Address

We have added the code necessary to configure MetaMask with the Hedera test network. Now, it's time to focus on adding the code that will allow us to connect our dApp to MetaMask.

In the `metamaskService.ts` file underneath _switchToHederaNetwork()_, add a new function named _connectToMetamask()._

```typescript
// returns a list of accounts
// otherwise empty array
export const connectToMetamask = async () => {
  const { ethereum } = window as any;
  // keep track of accounts returned
  let accounts: string[] = []
  if (!ethereum) {
    throw new Error("Metamask is not installed! Go install the extension!");
  }
  
  switchToHederaNetwork(ethereum);
  
  accounts = await ethereum.request({
    method: 'eth_requestAccounts',
  });
  return accounts;
}
```

If MetaMask is installed, _connectToMetamask()_ will call _switchToHederaNetwork()_ and submit a request to get accounts.

Before we do any kind of work or testing, it is important to ensure we are connected to the correct network.

***

## Install Dependencies

We’ve written the code to connect our application to MetaMask. Now it’s time to send HBAR to our MetaMask wallet.

Install the Hedera JavaScript SDK and dotenv by running the following command in the project root directory:

```bash
npm install --save @hashgraph/sdk dotenv
```

Create your `.env` file by adding a new file to the root directory of your react-app and naming it .env. Next, add the `.env` file to your `.gitignore` to prevent sharing your credentials in source control.

Your `.env` file will look like this:

```
REACT_APP_MY_ACCOUNT_ID=
REACT_APP_MY_PRIVATE_KEY=
```

In a react-app, the environment variables in your .env file must start with REACT\_APP\_. Set the values to the testnet account you created through the Hedera developer portal.

_**Note**: If you need to create a Hedera Testnet account, visit_ [_portal.hedera.com_](https://portal.hedera.com/) _and register to receive 10,000 testnet HBAR._

***

## Create Your Client

A client is used to communicate with the network. We create our client for the Hedera Testnet, which enables us to submit transactions and pay for them. Let’s create our client in our `Home.tsx` file.

```typescript
export default function Home() {
  // If we weren't able to grab it, we should throw a new error
  if (!process.env.REACT_APP_MY_ACCOUNT_ID || !process.env.REACT_APP_MY_PRIVATE_KEY) {
    throw new Error("Environment variables REACT_APP_MY_ACCOUNT_ID and REACT_APP_MY_PRIVATE_KEY must be present");
  }

  // create your client
  const myAccountId = AccountId.fromString(process.env.REACT_APP_MY_ACCOUNT_ID);
  const myPrivateKey = PrivateKey.fromString(process.env.REACT_APP_MY_PRIVATE_KEY);

  const client = Client.forTestnet();
  client.setOperator(myAccountId, myPrivateKey);

  return (
    <Stack 
      spacing={4}
      sx={{alignItems: 'center'}}
    >
      <Typography
        variant="h4"
        color="white"
      >
        Let's buidl a dApp on Hedera
      </Typography>
    </Stack>
  )
}
```

### Build Your TransferTransaction

In our services folder, add a new file and name it `hederaService.ts`. This file will hold the code to send HBAR to our MetaMask wallet. Inside the file, let’s create a function and name it _sendHbar_.

```typescript
import { AccountId, Client, PrivateKey, TransactionReceiptQuery, TransferTransaction } from "@hashgraph/sdk"

export const sendHbar = async (client:Client, fromAddress: AccountId | string, toAddress: AccountId | string, amount: number, operatorPrivateKey: PrivateKey) => {
  const transferHbarTransaction = new TransferTransaction()
    .addHbarTransfer(fromAddress, -amount)
    .addHbarTransfer(toAddress, amount)
    .freezeWith(client);

  const transferHbarTransactionSigned = await transferHbarTransaction.sign(operatorPrivateKey);
  const transferHbarTransactionResponse = await transferHbarTransactionSigned.execute(client);

  // Get the child receipt or child record to return the Hedera Account ID for the new account that was created
  const transactionReceipt = await new TransactionReceiptQuery()
    .setTransactionId(transferHbarTransactionResponse.transactionId)
    .setIncludeChildren(true)
    .execute(client);

  const childReceipt = transactionReceipt.children[0];

  if(!childReceipt || childReceipt.accountId === null) {
    console.warn(`No account id was found in child receipt. Child Receipt: ${JSON.stringify(childReceipt, null, 4)}`);
    return;
  }

   const newAccountId = childReceipt.accountId.toString();
   console.log(`Account ID of the newly created account: ${newAccountId}`);
}
```

This function builds a [TransferTransaction](https://docs.hedera.com/hedera/sdks-and-apis/sdks/cryptocurrency/transfer-cryptocurrency) that will send a specified amount of HBAR from one account to another. In our case, our **from** account is our developer portal account, and our **to** account is our MetaMask wallet address.

Once the transaction is built, we approve it by signing it with our private key by calling .sign(). We call .execute() on our signed transaction using the client to pay for the transaction fee.

When a transaction is executed, the result is of type [TransactionResponse](https://docs.hedera.com/hedera/sdks-and-apis/hedera-api/miscellaneous/transactionresponse). Use the transaction id from the TransactionResponse in a [TransactionReceiptQuery](https://docs.hedera.com/hedera/sdks-and-apis/sdks/transactions/get-a-transaction-receipt) to get a [TransactionReceipt](https://docs.hedera.com/hedera/sdks-and-apis/hedera-api/miscellaneous/transactionreceipt), including children transactions.

You can learn and read more about [parent and child transactions ](https://docs.hedera.com/hedera/core-concepts/transactions-and-queries#nested-transactions)on our documentation site.

_**Note: For security purposes, the account sending the tokens should be on a backend server, but for simplicity, it will be on the frontend. This is a reminder that private keys should never be exposed on the frontend, as that is the easiest way to lose control of your account.**_

***

## Send HBAR to MetaMask Wallet

We’ve written the code necessary to connect our application to MetaMask and installed the Hedera JavaScript SDK. Now it’s time to focus on connecting all the parts and sending HBAR to our MetaMask wallet.

### Configure State Management

React has a feature called [Context](https://beta.reactjs.org/learn/passing-data-deeply-with-context) that allows you to easily pass data between components without prop drilling. We will be leveraging this feature to save the MetaMask wallet address and enable us to access it from various components in our react-app.

Let's edit our `src/contexts/GlobalAppContext.tsx` file, which came with our template, to look like this:

```typescript
import { createContext, ReactNode, useState } from "react";

const defaultValue = {
  metamaskAccountAddress: '',
  setMetamaskAccountAddress: (newValue: string) => { },
}

export const GlobalAppContext = createContext(defaultValue)

export const GlobalAppContextProvider = (props: { children: ReactNode | undefined }) => {
  const [metamaskAccountAddress, setMetamaskAccountAddress] = useState('')

  return (
    <GlobalAppContext.Provider
      value={{
        metamaskAccountAddress,
        setMetamaskAccountAddress
      }}
    >
      {props.children}
    </GlobalAppContext.Provider>
  )
}
```

### Add Functionality to the NavBar Button

Once we’ve set up our context, we use it to share the application state throughout the app. We use the context by calling useContext() and passing in GlobalAppContext as an argument. This allows us to get and set the wallet address from anywhere in the app.

Add the following code to the top of the file `src/components/Navbar.tsx`:

```typescript
export default function NavBar() {
  // use the GlobalAppContext to keep track of the metamask account connection
  const { metamaskAccountAddress, setMetamaskAccountAddress } = useContext(GlobalAppContext);
```

Next, we will create a new function to connect to MetaMask and store the wallet address in the GlobalAppContext.

```typescript
const retrieveWalletAddress = async () => {
	const addresses = await connectToMetamask();
	if (addresses) {
      // grab the first wallet address
      setMetamaskAccountAddress(addresses[0]);
      console.log(addresses[0]);
    }
  }
```

Now we can add an onClick to our button and change the text to say 'Connect to MetaMask\\`. onClick will call retrieveWalletAddress.

Your completed button code will look like this:

```typescript
<Button
	variant='contained'
	color='secondary'
	sx={{
		ml: 'auto'
	}}
	onClick={retrieveWalletAddress}
>
	{metamaskAccountAddress === "" ?
		"Connect to MetaMask" :
		`Connected to: ${metamaskAccountAddress.substring(0, 8)}...`}
</Button>
```

### Add Send HBAR Button

In the `Home.tsx` file, we will call our global context to gain access to our metamaskAccountAddress state variable.

```typescript
export default function Home() {
  const { metamaskAccountAddress } = useContext(GlobalAppContext);
```

Next, add a new button to send HBAR to our MetaMask wallet. After the closing tag \</Typography>, create a material UI button and add onClick. The onClick will call sendHbar(), which is inside src/services/hederaService.ts.

```typescript
onClick={() => {
	sendHbar(client, myAccountId, AccountId.fromEvmAddress(0, 0, metamaskAccountAddress), 7, myPrivateKey)
}}
```

The **to address** will be the metamaskAccountAddress that we pulled out from our context using useContext(GlobalAppContext).

Your completed button code will look like this:

```typescript
<Stack 
  spacing={4}
  sx={{alignItems: 'center'}}
>
	<Typography
  		variant="h4"
  		color="white"
	>
  	  Let's buidl a dApp on Hedera
	</Typography>
	<Button
  	  variant="contained"
  	  color="secondary"
  	  onClick={() => {
    	sendHbar(client, myAccountId, AccountId.fromEvmAddress(0, 0, metamaskAccountAddress), 7, myPrivateKey)
  	  }}
	>
  	  Transfer HBAR to MetaMask Account
	</Button>
</Stack>
```

We’re ready to run our application! Open a terminal In the root directory of the project and run:

```bash
npm run start
```

Once up and running, click on the ‘Connect to MetaMask’ button in the upper right-hand corner.

<figure><img src="https://images.hedera.com/image1_2023-03-23-233319_nzot.png?w=1999&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680226514&#x26;s=840df12df5e70214fa46b884497ca668" alt=""><figcaption></figcaption></figure>

After you click on it, the MetaMask pop-up window will open, and you will be asked to switch from your previously connected network to the Hedera Testnet.

<figure><img src="https://images.hedera.com/image14.png?w=353&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680226506&#x26;s=e3466fba7e11adf8b32367d1fdf50cc0" alt=""><figcaption></figcaption></figure>

Choose the account that you would like to connect to this application.

_**Debugging tip: If you have previously connected your account to this dApp and clicking connect is not opening the wallet, disconnect all the connected accounts and then try again**_\

<figure><img src="https://images.hedera.com/image4_2023-03-24-024854_lcek.png?w=355&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680226527&#x26;s=32024236e053fa8f8ca2aea05922d3db" alt=""><figcaption></figcaption></figure>

Once connected, send HBAR by clicking on the ‘_SEND HBAR TO METAMASK_’ button. You can open your console and see that the transaction response is printed to the console.

<figure><img src="https://images.hedera.com/image7.png?w=360&#x26;auto=compress%2Cformat&#x26;fit=crop&#x26;dm=1680226531&#x26;s=fad7bfdfc0d0a3e10e05220d2eda86f7" alt=""><figcaption></figcaption></figure>

***

## Summary

You learned how to build a transfer transaction that sends an amount of HBAR through the Hedera Testnet to a MetaMask account. This can also be applied to other applications, and I encourage all to keep building.&#x20

Congratulations! 🎉 You successfully followed the tutorial to create an HBAR faucet for MetaMask and a Hedera React application that integrates with MetaMask! Feel free to reach out in [Discord](https://hedera.com/discord) if you have any questions!

***

## Additional Resources

**➡** [**TypeScript Project Repository**](https://github.com/a-ridley/hbar-faucet-for-metamask)

**➡** [**JavaScript Project Repository**](https://github.com/a-ridley/hbar-faucet-for-metamask-JS)

**➡** [**Download MetaMask Wallet**](https://metamask.io/download/)

**➡ Have a question? Ask on** [**StackOverflow**](https://stackoverflow.com/questions/tagged/hedera-hashgraph)

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Abi, DevRel Engineer</p><p><a href="https://github.com/a-ridley">GitHub</a> | <a href="https://www.linkedin.com/in/a-ridley/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/a-ridley/">https://www.linkedin.com/in/a-ridley/</a></td></tr><tr><td align="center"><p>Editor: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://twitter.com/theekrystallee">Twitter</a></p></td><td><a href="https://twitter.com/theekrystallee">https://twitter.com/theekrystallee</a></td></tr></tbody></table>
