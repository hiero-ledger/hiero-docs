# Local Environment Setup

This tutorial will help get your local environment setup and show you how to secure your setup for the local wallet pairings. You will also configure environment variables to use the Pinata IPFS API. By the end of this guide, you’ll have your local environment set up and configured to run the HashioDAO application locally.

## Prerequisites

- The [Vercel CLI](https://vercel.com/docs/cli) installed.
- A [Pinata](https://www.pinata.cloud/) account created.
- [Git command line](https://git-scm.com/downloads) and [TypeScript >= 4.7](https://www.npmjs.com/package/typescript) installed.

***

## Step 1: Project Installation

Open a new terminal and navigate to your preferred directory where you want your project to live. Clone the repo and install dependencies using these commands:

```bash
git clone https://github.com/hashgraph/hedera-accelerator-defi-dex-ui.git
yarn install
```

These commands clone the project repository onto your local machine and install all the necessary dependencies using the yarn package manager.

***

## Step 2: Local Environment Setup

#### Setup HTTPS for Local Wallet Pairing

The HashioDAO app utilizes the [hashconnect](https://github.com/Hashpack/hashconnect) library to pair with supported wallet extensions. Currently, the only supported wallet extension is [HashPack](https://www.hashpack.app/). The HashConnect 1-click pairing feature only works in an SSL secured environment (**https** URLs). Enable `HTTPS` in your local build by creating a `.env` file in the root of this project and adding the `HTTPS` environment variable to it.

Add the `HTTPS` environment variable to your `.env` file and set it to `true`:

{% code title=".env" %}

```
HTTPS=true
```

{% endcode %}

Create an SSL certificate. There are several tools that can be used to generate a certificate and key. An easy way to do this is to use the [mkcert](https://github.com/FiloSottile/mkcert) tool.

Install `mkcert` via Homebrew (on macOS):

{% code overflow="wrap" %}

```bash
# The [Homebrew](https://brew.sh/) macOS package manager is used for this example

# Install mkcert tool
brew install mkcert

# Install nss (only needed if you use Firefox)
brew install nss

# Setup mkcert on your machine (creates a CA)
mkcert -install
```

{% endcode %}

Generate the certificate and key, storing them in a `.cert` directory:

```bash
# Create a directory to store the certificate and key
mkdir -p .cert

# Generate the certificate (ran from the root of this project)
mkcert -key-file ./.cert/key.pem -cert-file ./.cert/cert.pem "localhost"
```

Set the `SSL_CRT_FILE` and `SSL_CRT_FILE` environment variables to the path of the certificate and key files. Add the variables to your `.env` file:

{% code title=".env" %}

```
/* Path to certificate */
SSL_CRT_FILE=./.cert/cert.pem

/* Path to key */
SSL_KEY_FILE=./.cert/key.pem
```

{% endcode %}

{% hint style="info" %}
**Note**: Make sure to include `.env` and `.cert` in your `.gitignore` file so this information is not committed to version control.
{% endhint %}

#### Setup Pinata Environment Variables

The HashioDAO app stores and retrieves IPFS data using Pinata. A Pinata public key, secret key, and gateway URL are necessary for IPFS pinning and fetching features to work as intended. If you have not already done so, create a Pinata account to generate a new set of keys and a gateway URL.

Add the below environment variables to your `.env` file to use the Pinata IPFS API:

{% code title=".env" %}

```
PRIVATE_PINATA_API_KEY=/** Public Key **/
PRIVATE_PINATA_API_SECRET_KEY=/** Secret Key **/
VITE_PUBLIC_PINATA_GATEWAY_URL=/** Gateway URL **/
```

{% endcode %}

A more comprehensive tutorial can be found in the [Pinata API Docs](https://docs.pinata.cloud/docs/welcome-to-pinata).

***

## Step 3: Run Application

Run the application using the below command:

```bash
vercel dev
```

This command will start your application, and you should see an `https://` prefixed URL for your local server, indicating that HTTPS is successfully enabled.

***

## Additional Resources

➡ [**HashioDAO Repository**](https://github.com/hashgraph/hedera-accelerator-defi-dex-ui)

➡ [**Pinata API Documentation**](https://docs.pinata.cloud/introduction)

➡ [**HashPack Documentation**](https://docs.hashpack.app/dapp-developers/hashconnect)
