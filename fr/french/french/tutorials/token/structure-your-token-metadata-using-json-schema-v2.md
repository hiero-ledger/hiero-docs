# Structure Your Token Metadata Using JSON Schema V2

## Summary

When you create a new fungible or non-fungible token, you have the ability to add metadata. It's common to add metadata for NFTs, but also fungible tokens. The biggest problem with metadata is that it's often unstructured or doesn't follow a set of guidelines.

Therefore, Hedera has developed the ["Token Metadata JSON Schema V2](https://github.com/hashgraph/hedera-improvement-proposal/blob/main/HIP/hip-412.md#reference-implementation)" for developers and creators who want to structure their metadata in an organized way. The biggest benefit of using this community-accepted standard is that most of the tooling on the Hedera network can scrape and interpret your metadata, like NFT explorers listing rarity attributes based on your metadata.

***

## Prerequisites

We recommend you complete one of the two tutorials below that teach you how to create a fungible or non-fungible token on the Hedera network.

{% content-ref url="create-and-transfer-your-first-nft.md" %}
[create-and-transfer-your-first-nft.md](create-and-transfer-your-first-nft.md)
{% endcontent-ref %}

{% content-ref url="create-and-transfer-your-first-fungible-token.md" %}
[create-and-transfer-your-first-fungible-token.md](create-and-transfer-your-first-fungible-token.md)
{% endcontent-ref %}

***

## Table of Contents

1. [Connect Metadata](structure-your-token-metadata-using-json-schema-v2.md#how-do-you-connect-metadata-to-a-token)
2. [Metadata Schema](structure-your-token-metadata-using-json-schema-v2.md#what-does-the-token-metadata-json-schema-v2-look-like)
3. [Verify Metadata](structure-your-token-metadata-using-json-schema-v2.md#how-to-verify-your-token-metadata-is-correct)
4. [Video Tutorial](structure-your-token-metadata-using-json-schema-v2.md#want-to-learn-more-about-token-metadata)

***

## How do you connect metadata to a token?

It's essential to understand that the token metadata JSON schema V2 requires you to store metadata using a storage solution, centralized or decentralized, such as IPFS or Arweave.

When creating a non-fungible token using the Hedera Token Service, you set the metadata value to the metadata JSON file to define your NFT, wherever it’s stored. This technique allows you to connect the metadata to the token created on the Hedera network. The "memo" or "symbol" fields are not allowed on the NFT.

An excerpt from the [NFT minting tutorial](https://docs.hedera.com/hedera/tutorials/token-service/create-and-transfer-your-first-nft#2.-mint-a-new-nft) shows this connection when minting a new NFT.

{% tabs %}
{% tab title="Java" %}

<pre class="language-java"><code class="lang-java"><strong>// IPFS content identifier (CID) that points to your metadata
</strong>String CID = ("QmTzWcVfk88JRqjTpVwHzBeULRTNzHY7mnBSG42CpwHmPa") ;

// Mint a new NFT
TokenMintTransaction mintTx = new TokenMintTransaction()
        .setTokenId(tokenId)
        .addMetadata(CID.getBytes())
	.freezeWith(client);
</code></pre>

{% endtab %}

{% tab title="JavaScript" %}

```javascript
// IPFS content identifier (CID) that points to your metadata
let CID = "ipfs://QmTzWcVfk88JRqjTpVwHzBeULRTNzHY7mnBSG42CpwHmPa";

// Mint new NFT
let mintTx = await new TokenMintTransaction()
    .setTokenId(tokenId)
    .setMetadata([Buffer.from(CID)])
    .freezeWith(client);
```

{% endtab %}

{% tab title="Go" %}

<pre class="language-go"><code class="lang-go"><strong>// IPFS content identifier (CID) that points to your metadata
</strong><strong>CID := "QmTzWcVfk88JRqjTpVwHzBeULRTNzHY7mnBSG42CpwHmPa"
</strong>
//Mint new NFT
mintTx, err := hedera.NewTokenMintTransaction().
	SetTokenID(tokenId).
	SetMetadata([]byte(CID)).
	FreezeWith(client)
</code></pre>

{% endtab %}
{% endtabs %}

***

## What does the Token Metadata JSON schema V2 look like?

First of all, you can find the full reference implementation of this JSON schema here:

- Hedera Improvement Proposals [GitHub Repository](https://github.com/hashgraph/hedera-improvement-proposal/blob/main/HIP/hip-412.md#default-schema-collectibe-hedera-nfts-format-hip412100)
- [NFT.Storage (gateway link)](https://nftstorage.link/ipfs/bafkreidcsqzr5su356thecwuyzrhsgekfdsqzuyuqxtsu4vh7oc34iv5oy)
- IPFS: ipfs://bafkreidcsqzr5su356thecwuyzrhsgekfdsqzuyuqxtsu4vh7oc34iv5oy

Let's take a look at the different fields you can specify.

### Required fields:

{% tabs %}
{% tab title="name, type, and image" %}
The schema defines three required fields:

- **name**: Full name of the NFT
- **type**: [MIME type](https://github.com/hashgraph/hedera-improvement-proposal/blob/main/HIP/hip-412.md#mime-formatting) for the image
- **image**: A URI pointing to an image (decentralized or centralized storage).

The `image` field can both serve as a preview or full-resolution image for your NFT to ensure cross-platform compatibility. The image field will be displayed in wallets and marketplaces by default.

Creators are recommended to point to a thumbnail in the `image` field and put the high-resolution image in the `files` array with the `is_default_file` boolean set to indicate that this file represents the default image for the NFT. (This is shown in the next section)

Here's a small example of an implementation.

```json
{
    "name": "My first NFT",
    "type": "image/png",
    "image": "https://myserver.com/preview-image-nft-001.png"
}
```

{% endtab %}
{% endtabs %}

### Optional fields:

{% tabs %}
{% tab title="files" %}
The `files` field represents an array containing file objects. For collectible NFTs, the files array allows you to store the high-resolution image of your NFT. However, you can also use this field for multi-file NFTs. Each file object requires a **URI** and **type.**

It's recommended to use the **is\_default\_file** field to indicate which file is the main file for your NFT. Besides that, the files array allows you to upload or link file-specific metadata. This allows you to nest files indefinitely.

```json
{
    "name": "My first NFT",
    "type": "image/png",
    "image": "https://myserver.com/preview-image-nft-001.png",
    "files": [
        {
            "uri": "https://myserver.com/high-resolution-nft-001.png",
            "checksum": "9defbb6402d4bf39f2ea580099c73194647b24a659b6f6b778e3dd71755b8862",
            "is_default_file": true,
            "type": "image/png"
        }
    ]
}
```

{% endtab %}

{% tab title="properties" %}
Additional fields are not allowed to be defined at the root level of the metadata object.

```json
// ❌ Not allowed to add "website" to the root of the object
{
    "website": "https://mysite.com",
    "name": "My first NFT",
    "type": "image/png",
    "image": "https://myserver.com/preview-image-nft-001.png"
}
```

If you want to add custom fields, you can add them to the `properties` object. For example, you are linking to a website or social media pages. You can structure the metadata within the `properties` field as needed. Some developers even prefer defining their own standard for the properties field, not for the entire metadata object.

<pre class="language-json"><code class="lang-json"><strong>// ✅ Good example
</strong><strong>{
</strong>    "name": "My first NFT",
    "type": "image/png",
    "image": "https://myserver.com/preview-image-nft-001.png",
    "properties": {
        "website": "https://mysite.com",
        "socials": {
            "linkedin": "https://www.linkedin.com/in/myprofile/"
        }
    }
}
</code></pre>

{% endtab %}

{% tab title="attributes" %}
The `attributes` field is specifically used to calculate the rarity of NFTs. It's an industry-accepted way to define traits and their values for a collectible NFT collection in order to calculate the rarity score of the NFT.

The `attributes` field consists of an array of `attribute` objects. This is the structure of such an object:

- **trait\_type**: (required) Name of the trait.
- **value**: (required) Value for the trait, e.g., for a `trait_type = clothing`, values can be `pants`, `shirt`, or `t-shirt`.
- **display\_type**: (optional) Allows you to specify how the trait should be displayed. For instance, you can set it to `datetime` so the person or bot knows the value should be interpreted as a date.
- **max\_value**: (optional) It's possible to set a `max_value` for a numerical value.

<pre class="language-json"><code class="lang-json">{
    "name": "My first NFT",
    "type": "image/png",
    "image": "https://myserver.com/preview-image-nft-001.png",
    "attributes": [
    	{
    		"trait_type": "clothing",
    		"value": "pants"
    	},
<strong>    	{
</strong>		"trait_type": "color",
		"display_type": "color",
		"value": "rgb(255,0,0)"
	},
	{
		"trait_type": "hasPipe",
		"display_type": "boolean",
		"value": true
	},
	{
		"trait_type": "coolness",
		"display_type": "boost",
		"value": 10,
		"max_value": 100
	},
	{
		"trait_type": "stamina",
		"display_type": "percentage",
		"value": 83
	},
	{
		"trait_type": "birth",
		"display_type": "datetime",
		"value": 732844800
	}
    ]
}
</code></pre>

**Extra resources:** You can find all information about the `attributes` field in the detailed [schema specification](https://github.com/hashgraph/hedera-improvement-proposal/blob/main/HIP/hip-412.md#attributestrait\_type).
{% endtab %}

{% tab title="localization" %}
The standard also allows for localization. Each locale links to another metadata file containing localized metadata and files. This allows for a clean metadata structure. Don't define a new localization object for a localized metadata file to avoid infinite looping when parsing an NFT's metadata file.

Note that the `localization.uri` property contains `{locale}`. The `{locale}` part references a locale in the `locales` array. You should use two-letter language codes according to the [ISO 639-1 standard](https://en.wikipedia.org/wiki/List\_of\_ISO\_639-1\_codes) to define languages.

```json
{
    "name": "My first NFT",
    "type": "image/png",
    "image": "https://myserver.com/preview-image-nft-001.png",
    "localization": {
        "uri": "ipfs://QmWS1VAdMD353A6SDk9wNyvkT14kyCiZrNDYAad4w1tKqT/{locale}.json",
	"default": "en",
	"locales": ["es", "fr"]
    }
}
```

In this example, the default locale is set to English (`en`) and the schema provides localization for Spanish (`es`) and French (`fr`). The schema assumes it can find the Spanish and French version under the following URIs because of the way it has been specified using the interpolation notation `{locale}.json`. This is what the resulting URIs should look like:

```
ipfs://QmWS1VAdMD353A6SDk9wNyvkT14kyCiZrNDYAad4w1tKqT/es.json
ipfs://QmWS1VAdMD353A6SDk9wNyvkT14kyCiZrNDYAad4w1tKqT/fr.json
```

A localized file would have the same structure, but doesn't specify any localization field. Here's an example for the French version.

<pre class="language-json"><code class="lang-json"><strong>// metadata for ipfs://QmWS1VAdMD353A6SDk9wNyvkT14kyCiZrNDYAad4w1tKqT/fr.json
</strong><strong>{
</strong>    "name": "Mon NFT (French)",
    "type": "image/png",
    "image": "https://myserver.com/preview-image-nft-001.png"
}
</code></pre>

{% endtab %}

{% tab title="description, creator, creatorDID, checksum, and format" %}
Here's a list of optional fields you can define according to the Token Metadata JSON Schema V2 specification.

- **description**: A text that describes your token or NFT collection.
- **creator**: Identifies the artist name(s).
- **creatorDID**: Points to a decentralized identifier to identify the creator.
- **checksum:** Cryptographic SHA-256 hash of the representation of the `image` resource or resources in the `files` array. It allows browsers or other tooling to verify the integrity of any file you list.
- **format:** Indicates the implemented metadata schema specification. Currently, the default version (Token Metadata JSON Schema V2) is represented by `HIP412@2.0.0`. If you wonder why, each update to the JSON Schema requires a new Hedera Improvement Proposal (HIP). Therefore, the `format` field lists the HIP number and the associated version of the Token Metadata JSON Schema.
  {% endtab %}
  {% endtabs %}

### Putting things together:

{% tabs %}
{% tab title="Full schema implementation" %}
This is what a full Token Metadata JSON Schema V2 specification looks like.

```json
{
	"name": "Example NFT 001",
	"creator": "Jane Doe, John Doe",
	"creatorDID": "did:hedera:mainnet:7Prd74ry1Uct87nZqL3ny7aR7Cg46JamVbJgk8azVgUm;hedera:mainnet:fid=0.0.123",
	"description": "This describes my NFT",
	"image": "https://myserver.com/preview-image-nft-001.png",
	"checksum": "ba7816bf8f01cfea414140de5dae2223b00361a396177a9cb410ff61f20015ad",
	"type": "image/png",
	"format": "HIP412@2.0.0",
	"properties": {
		"external_url": "https://nft.com/mycollection/001"
	},
	"files": [
		{
			"uri": "https://myserver.com/high-resolution-nft-001.png",
			"checksum": "9defbb6402d4bf39f2ea580099c73194647b24a659b6f6b778e3dd71755b8862",
			"is_default_file": true,
			"type": "image/png"
		},
		{
			"uri": "ipfs://yusopwpksaioposjfopiapnnjlsl",
			"type": "image/png"
		}
	],
	"attributes": [
		{
    			"trait_type": "clothing",
    			"value": "pants"
    		},
		{
			"trait_type": "birth",
			"display_type": "datetime",
			"value": 732844800
		}
	],
	"localization": {
		"uri": "ipfs://QmWS1VAdMD353A6SDk9wNyvkT14kyCiZrNDYAad4w1tKqT/{locale}.json",
		"default": "en",
		"locales": ["es", "fr"]
	}
}
```

{% endtab %}
{% endtabs %}

***

## How do you verify your token metadata is correct?

When you create token metadata for the first time, you want to verify your metadata against the Token Metadata JSON Schema V2. To help you, Hedera has created an [NFT utilities SDK](https://github.com/hashgraph/hedera-nft-utilities#token-metadata-validator) **(only for JavaScript)** to verify your metadata against the JSON Schema V2.

You can install the package using Yarn or NPM.

```bash
npm i -s @hashgraph/nft-utilities
```

Next, you need to import the `validator` function that accepts your metadata as a JSON object and the schema version against which you want to verify the metadata (`2.0.0`). Here's the code.

```javascript
const metadata = {
    attributes: [
        { trait_type: "Background", value: "Yellow" }
    ],
    creator: "NFT artist",
};
const version = '2.0.0';

const issues = validator(metadata, version);
console.log(issues);
```

The package will return errors and warnings using the below interface. This snippet of example output tells you that you have incorrectly used the `percentage` `display_type` in the attributes field, and you have defined a custom field called `imagePreview` on the root of the metadata object, which is not allowed (use the `properties` field).

```json
{
    "errors": [
        {
            "type": "attribute",
            "msg": "Trait stamina of type 'percentage' must be between [0-100], found 157",
            "path": "instance.attributes[0]"
        }
    ],
    "warnings": [
        {
            "type": "schema",
            "msg": "is not allowed to have the additional property 'imagePreview'",
            "path": "instance"
        }
    ]
}
```

***

## Want to learn more about token metadata?

Here's a video about how crucial structuring your token metadata is and how to do it according to Token Metadata JSON Schema V2.

{% embed url="https://www.youtube.com/watch?v=o8lY5nQ7pYo" %}

You can find **examples** in this [blog post](https://hedera.com/blog/hedera-nft-metadata-hip412) in the section "Token Metadata V2 NFT Examples". If you still have questions, reach out on [Discord](https://hedera.com/discord) or ask it on [StackOverflow](https://stackoverflow.com/questions/tagged/hedera-hashgraph).

Besides that, you can **read up on the full implementation** of token metadata in the Hedera Improvement Proposals [GitHub Repository under HIP-412](https://github.com/hashgraph/hedera-improvement-proposal/blob/main/HIP/hip-412.md).

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><p>Writer: Michiel, Developer Advocate</p><p><a href="https://github.com/michielmulders">GitHub</a> | <a href="https://www.linkedin.com/in/michielmulders/">LinkedIn</a></p></td><td><a href="https://www.linkedin.com/in/michielmulders/">https://www.linkedin.com/in/michielmulders/</a></td></tr><tr><td align="center"><p>Editor: Krystal, Technical Writer</p><p><a href="https://github.com/theekrystallee">GitHub</a> | <a href="https://twitter.com/theekrystallee">Twitter</a></p></td><td><a href="https://twitter.com/theekrystallee">https://twitter.com/theekrystallee</a></td></tr></tbody></table>
