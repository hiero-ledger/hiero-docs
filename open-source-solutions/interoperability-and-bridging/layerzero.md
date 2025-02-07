# LayerZero

## What is LayerZero?

LayerZero is an omnichannel interoperability protocol designed to facilitate cross-chain communication. By enabling secure and efficient message passing across chains, LayerZero allows developers to build [decentralized applications (dApps)](../../support-and-community/glossary.md#decentralized-application-dapp) that operate cohesively over multiple blockchains. This capability enhances the functionality and user experience of dApps by leveraging the unique features of various ecosystems.

***

## Overview of LayerZero and HTS Compatibility

LayerZero integrates seamlessly with the Hedera Token Service (HTS) and EVM-compatible tokens (ERC-20 and ERC-721). This integration enables efficient cross-chain communication for various token types, making it easier to bridge assets such as $USDC or $Sauce between Hedera and other networks.&#x20;

***

## Getting Started with LayerZero on Hedera&#x20;

To get started quickly, you can begin with the Gitpod demo, which requires no environment setup. Alternatively, you can go directly into the [LayerZero Quickstart series](https://docs.layerzero.network/v2/developers/evm/getting-started). These guides provide an overview of deploying an Omnichain Application (OApp) on Hedera and other EVM-compatible networks, covering essentials like setting up your LayerZero environment and deploying contracts for cross-chain messaging.

<table data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p><strong>➡</strong> <a href="https://github.com/hedera-dev/hedera-example-layer-zero-bridging-oapp?tab=readme-ov-file#try-it-in-gitpod"><strong>GITPOD DEMO</strong></a></p><p>A no-setup-required demo to experience LayerZero</p></td><td><a href="https://github.com/hedera-dev/hedera-example-layer-zero-bridging-oapp?tab=readme-ov-file#try-it-in-gitpod">https://github.com/hedera-dev/hedera-example-layer-zero-bridging-oapp?tab=readme-ov-file#try-it-in-gitpod</a></td></tr><tr><td align="center"><p><strong>➡</strong> <a href="https://docs.layerzero.network/v2/developers/evm/oapp/overview"><strong>LAYERZERO QUICKSTART</strong></a></p><p>A step-by-step guide for deploying an omnichain application.</p></td><td><a href="https://docs.layerzero.network/v2/developers/evm/oapp/overview">https://docs.layerzero.network/v2/developers/evm/oapp/overview</a></td></tr><tr><td align="center"><p><strong>➡</strong> <a href="https://github.com/hashgraph/hedera-smart-contracts/tree/main/lib/layer-zero#layer-zero-examples"><strong>LAYERZERO EXAMPLES REPO</strong></a></p><p>Explore examples for bridging tokens with LayerZero.</p></td><td><a href="https://github.com/hashgraph/hedera-smart-contracts/tree/main/lib/layer-zero#layer-zero-examples">https://github.com/hashgraph/hedera-smart-contracts/tree/main/lib/layer-zero#layer-zero-examples</a></td></tr></tbody></table>

***

## LayerZero Examples and Key Components

{% hint style="warning" %}
All examples in the demo repo are exploratory code and have NOT been audited. Please use it at your own risk!
{% endhint %}

### Omnichain Fungible Token (OFT) / Omnichain Non-Fungible Token (ONFT)

The OFT and ONFT token standards allow the transfer of fungible (ERC-20s) and non-fungible tokens (ERC-721s) across chains using LayerZero's messaging infrastructure. If you're bridging an existing fungible ERC token on Hedera to another EVM chain, you can deploy the [**OFT**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/lib/layer-zero#oft) standard contract and the [**ONFT**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/lib/layer-zero#onft) standard contract for NFTs.&#x20;

#### **Use Case**

Allows fungible and non-fungible tokens to be transferred across chains.

**Example Contracts**

{% tabs %}
{% tab title="OFT" %}
{% @github-files/github-code-block url="https://github.com/hashgraph/hedera-smart-contracts/blob/main/lib/layer-zero/ExampleOFT.sol" %}
{% endtab %}

{% tab title="ONFT" %}
{% @github-files/github-code-block url="https://github.com/hashgraph/hedera-smart-contracts/blob/main/lib/layer-zero/ExampleONFT.sol" %}
{% endtab %}
{% endtabs %}

{% hint style="info" %}
**Considerations**

* HTS tokens can function as ERC-compatible assets through an OFT adapter, requiring no modifications.
* OFT standard token contracts must be deployed on the destination chain.
{% endhint %}

### OFT Adapter / ONFT Adapter

The OFT Adapter acts as an intermediary contract that handles sending and receiving existing fungible tokens across chains. If your token already exists on the chain you want to connect to, you can deploy the [**OFT Adapter**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/lib/layer-zero#oft-adapter) contract to act as an intermediary lockbox for the token. Similarly, the ONFT Adapter handles sending and receiving existing fungible tokens across chains. If your NFT already exists on the chain you want to connect to, you can deploy the [**ONFT Adapter**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/lib/layer-zero#onft-adapter) contract to act as an intermediary lockbox for the NFT.&#x20;

#### **Use Case**

Intermediary contract that handles sending and receiving existing fungible tokens across chains. If your token already exists on the chain you want to connect to, you can deploy the OFT Adapter contract to act as an intermediary lockbox for the token.

**Example Contracts**

{% tabs %}
{% tab title="OFT Adapter" %}
{% @github-files/github-code-block url="https://github.com/hashgraph/hedera-smart-contracts/blob/main/lib/layer-zero/ExampleOFTAdapter.sol" %}
{% endtab %}

{% tab title="ONFT Adapter" %}
{% @github-files/github-code-block url="https://github.com/hashgraph/hedera-smart-contracts/blob/main/lib/layer-zero/ExampleONFTAdapter.sol" %}


{% endtab %}
{% endtabs %}

{% hint style="info" %}
**Considerations**

* HTS tokens can function as ERC-compatible assets through an OFT adapter, requiring no modifications.
{% endhint %}

### HTS Connector

The [**HTS Connector**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/lib/layer-zero#hts-connector) extends LayerZero’s functionality to accommodate HTS tokens, addressing the differences between HTS and ERC token standards. It facilitates the integration of existing ERC tokens with Hedera by bridging them as native HTS tokens. This is a variant of OFT when bringing tokens to Hedera as HTS. If you bring a token to Hedera as an ERC token, you can use [**OFT**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/lib/layer-zero#oft) or [**ONFT**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/lib/layer-zero#onft).

#### **Use Case**

Bridging a token from other networks to Hedera as an HTS token.

{% hint style="info" %}
**Considerations:**

* When deploying HTS connector:
  * The contract should be associated to the token or be deployed with `maxAutomaticAssociation`
  * The "supply key" of the token must contain the address of the HTS Connector contract.
{% endhint %}

<details>

<summary><strong>Example HTS Connector Contracts</strong></summary>

```solidity
// SPDX-License-Identifier: Apache-2.0
pragma solidity ^0.8.20;

import {OFTCore} from "@layerzerolabs/lz-evm-oapp-v2/contracts/oft/OFTCore.sol";
import "./hts/HederaTokenService.sol";
import "./hts/IHederaTokenService.sol";
import "./hts/KeyHelper.sol";

/**
 * @title HTS Connector
 * @dev HTS Connector is a HTS token that extends the functionality of the OFTCore contract.
    */
abstract contract HTSConnector is OFTCore, KeyHelper, HederaTokenService {
    address public htsTokenAddress;
    bool public finiteTotalSupplyType = true;
    event TokenCreated(address tokenAddress);

    /**
     * @dev Constructor for the HTS Connector contract.
     * @param _name The name of HTS token
     * @param _symbol The symbol of HTS token
     * @param _lzEndpoint The LayerZero endpoint address.
     * @param _delegate The delegate capable of making OApp configurations inside of the endpoint.
     */
    constructor(
        string memory _name,
        string memory _symbol,
        address _lzEndpoint,
        address _delegate
    ) payable OFTCore(8, _lzEndpoint, _delegate) {
        IHederaTokenService.TokenKey[] memory keys = new IHederaTokenService.TokenKey[](1);
        keys[0] = getSingleKey(
            KeyType.SUPPLY,
            KeyValueType.INHERIT_ACCOUNT_KEY,
            bytes("")
        );

        IHederaTokenService.Expiry memory expiry = IHederaTokenService.Expiry(0, address(this), 8000000);
        IHederaTokenService.HederaToken memory token = IHederaTokenService.HederaToken(
            _name, _symbol, address(this), "memo", finiteTotalSupplyType, 5000, false, keys, expiry
        );

        (int responseCode, address tokenAddress) = HederaTokenService.createFungibleToken(
            token, 1000, int32(int256(uint256(8)))
        );
        require(responseCode == HederaTokenService.SUCCESS_CODE, "Failed to create HTS token");

        int256 transferResponse = HederaTokenService.transferToken(tokenAddress, address(this), msg.sender, 1000);
        require(transferResponse == HederaTokenService.SUCCESS_CODE, "HTS: Transfer failed");

        htsTokenAddress = tokenAddress;

        emit TokenCreated(tokenAddress);
    }

    /**
     * @dev Retrieves the address of the underlying HTS implementation.
     * @return The address of the HTS token.
     */
    function token() public view returns (address) {
        return htsTokenAddress;
    }

    /**
     * @notice Indicates whether the HTS Connector contract requires approval of the 'token()' to send.
     * @return requiresApproval Needs approval of the underlying token implementation.
     */
    function approvalRequired() external pure virtual returns (bool) {
        return false;
    }

    /**
     * @dev Burns tokens from the sender's specified balance.
     * @param _from The address to debit the tokens from.
     * @param _amountLD The amount of tokens to send in local decimals.
     * @param _minAmountLD The minimum amount to send in local decimals.
     * @param _dstEid The destination chain ID.
     * @return amountSentLD The amount sent in local decimals.
     * @return amountReceivedLD The amount received in local decimals on the remote.
     */
    function _debit(
        address _from,
        uint256 _amountLD,
        uint256 _minAmountLD,
        uint32 _dstEid
    ) internal virtual override returns (uint256 amountSentLD, uint256 amountReceivedLD) {
        (amountSentLD, amountReceivedLD) = _debitView(_amountLD, _minAmountLD, _dstEid);

        int256 transferResponse = HederaTokenService.transferToken(htsTokenAddress, _from, address(this), int64(uint64(_amountLD)));
        require(transferResponse == HederaTokenService.SUCCESS_CODE, "HTS: Transfer failed");

        (int256 response,) = HederaTokenService.burnToken(htsTokenAddress, int64(uint64(amountSentLD)), new int64[](0));
        require(response == HederaTokenService.SUCCESS_CODE, "HTS: Burn failed");
    }

    /**
     * @dev Credits tokens to the specified address.
     * @param _to The address to credit the tokens to.
     * @param _amountLD The amount of tokens to credit in local decimals.
     * @dev _srcEid The source chain ID.
     * @return amountReceivedLD The amount of tokens ACTUALLY received in local decimals.
     */
    function _credit(
        address _to,
        uint256 _amountLD,
        uint32 /*_srcEid*/
    ) internal virtual override returns (uint256) {
        (int256 response, ,) = HederaTokenService.mintToken(htsTokenAddress, int64(uint64(_amountLD)), new bytes[](0));
        require(response == HederaTokenService.SUCCESS_CODE, "HTS: Mint failed");

        int256 transferResponse = HederaTokenService.transferToken(htsTokenAddress, address(this), _to, int64(uint64(_amountLD)));
        require(transferResponse == HederaTokenService.SUCCESS_CODE, "HTS: Transfer failed");

        return _amountLD;
    }
}
```

</details>

***

## Developer Considerations to Note EVM Differences

Please note the smallest unit of _**HBAR is the tinybar (8 decimal places)**_, while the _**JSON-RPC relay operates 18 decimal places**_ for compatibility with Ethereum tools. This means when dealing with `msg.value`, conversions between tinybars and weibars are necessary. Additionally, [_Hedera’s gas model_](https://docs.hedera.com/hedera/core-concepts/smart-contracts/gas-and-fees#gas-reservation-and-unused-gas-refund) _charges for at least 80% of gas_, regardless of usage, and event handling often requires querying mirror nodes. Please take these differences into account, especially when calling `quote`. Reference the  [Understanding Hedera's EVM Differences and Compatibility: For EVM Developers](../../core-concepts/smart-contracts/understanding-hederas-evm-differences-and-compatibility/for-evm-developers-migrating-to-hedera/) section for a more comprehensive list of differences.

***

## Additional Resources

* [**LZ Examples Repo**](https://github.com/hashgraph/hedera-smart-contracts/tree/main/lib/layer-zero#layer-zero-examples)
* [**Demo Code Repo**](https://github.com/hedera-dev/hedera-example-layer-zero-bridging-oapp)
* [**LayerZero Scan**](https://layerzeroscan.com/)
* [**Hedera Fee Estimator**](https://hedera.com/fees)
* [**LayerZero Developer Resources**](https://layerzero.network/developers)
* [**Hedera Testnet LayerZero Endpoint**](https://docs.layerzero.network/v2/developers/evm/technical-reference/deployed-contracts#hedera-testnet)
