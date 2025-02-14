---
description: The mirror node REST API offers the ability to query transaction information.
---

# Mirror Node REST API

## Overview

The **Hedera Mirror Node REST API** enables developers to access historical transaction data from Hedera's mainnet, testnet, and previewnet. Each transaction generates a record stored in a record file, which can be accessed via the Mirror Node REST APIs.

***

## Making a Request

To make a request, use the network endpoint and the REST API of choice.&#x20;

For example, To get a list of transactions on the **Hedera mainnet**, use the following API endpoint:

```bash
https://mainnet.mirrornode.hedera.com/api/v1/transactions
```

This will return a JSON response containing transaction details such as transaction ID, timestamps, and fees.

If you are using `curl`, you can make a request like this:

```bash
curl -X GET "https://mainnet.mirrornode.hedera.com/api/v1/transactions" -H "Accept: application/json"
```

***

## Hedera Mirror Node Swagger UI Environments

Hedera provides interactive Swagger UI for its Mirror Node REST API. Developers can use these environments to explore available endpoints, check request parameters, and test API calls without writing code.

#### **Available Environments:**

<table><thead><tr><th width="217">Network</th><th>Swagger UI URL</th></tr></thead><tbody><tr><td><strong>Mainnet</strong></td><td><a href="https://mainnet-public.mirrornode.hedera.com/api/v1/docs/#/">Mainnet Swagger UI</a></td></tr><tr><td><strong>Testnet</strong></td><td><a href="https://testnet.mirrornode.hedera.com/api/v1/docs/#/">Testnet Swagger UI</a></td></tr><tr><td><strong>Previewnet</strong></td><td><a href="https://previewnet.mirrornode.hedera.com/api/v1/docs/#/">Previewnet Swagger UI</a></td></tr></tbody></table>

***

## **Hedera Mirror Node Environments**

Hedera provides three different network environments for interacting with the Mirror Node API.

<table><thead><tr><th width="212">Network</th><th>Base URL</th></tr></thead><tbody><tr><td><strong>Mainnet</strong></td><td><code>https://mainnet.mirrornode.hedera.com/</code></td></tr><tr><td><strong>Testnet</strong></td><td><code>https://testnet.mirrornode.hedera.com/</code></td></tr><tr><td><strong>Previewnet</strong></td><td><code>https://previewnet.mirrornode.hedera.com/</code></td></tr></tbody></table>

Each of these base URLs serves as the foundation for all API calls.&#x20;

You may also check out [Validation Cloud](https://validationcloud.io/), [DragonGlass](https://app.dragonglass.me/hedera/pricing), or [Arkhia](https://www.arkhia.io/features/#api-services) as alternatives.‌

{% hint style="warning" %}
Public mainnet mirror node requests per second (RPS) are currently throttled at **50 per IP address**. These configurations may change in the future depending on performance or security considerations. At this time, no authentication is required.
{% endhint %}
