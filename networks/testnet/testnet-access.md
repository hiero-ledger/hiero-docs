# Testnet Accounts

You will need a Hedera **testnet account** or **preview testnet** **account** to interact with and pay for any of the network services (cryptocurrency, consensus, tokens, files, and smart contracts). Your Hedera testnet account is what holds a balance of _HBAR_ to be used for transfers to other accounts or payments for network services.

### Step 1: Create Hedera Portal Profile (Faucet)

To create your Hedera Portal profile, register [here](https://portal.hedera.com/register) and complete your profile. The Hedera Portal works like a regular faucet. The Portal will top up your account's balance daily to 10,000 testnet _HBAR_.

Once you've completed setting up your profile, select the test network from the network drop-down menu and create an account. You can easily copy your `accountId`, `public key`, and `private key` information to your clipboard to use when configuring your SDK environment for testnet. The "NETWORK" section contains the node ID and node address of testnet nodes that can be configured in your application to submit transactions.

{% hint style="info" %}
_**Note:** When previewnet or testnet is reset, new account IDs will be generated. The public and private key pair remain consistent during previewnet and testnet resets. If you receive an invalid account ID response from the network it is likely you need to update your previewnet or testnet account ID._
{% endhint %}

![](../../.gitbook/assets/testnet.png)

You're now ready to build your application on testnet!
