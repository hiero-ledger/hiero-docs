# One Click Mirror Node Deployment

## Google Cloud Platform Marketplace

Deploy your own mirror node with just a few clicks! The Hedera Mirror Node is open-source software and does not carry an associated license or deployment fee. However, users are responsible for paying for the compute resource used, the data retrieved from Google Cloud Storage buckets, and any other Google Cloud Platform services used during this deployment.\&#x20

► Before you proceed to the next step, obtain the GCP requester pay information:

<details>

<summary>How To Obtain Google Cloud Platform Requester Pay Information</summary>

In this step, you will generate your Google Cloud Platform HMAC access keys. These keys are needed to authenticate requests between your machine and Google Cloud Storage. They are similar to a username and password. Follow these steps to retrieve your **access key, secret**, and **project ID**:

- Create a new [project](https://cloud.google.com/resource-manager/docs/creating-managing-projects) and link your [billing account](https://cloud.google.com/billing/docs/how-to/manage-billing-account).
- From the left navigation bar, select **Cloud Storage > Settings.**
- Click the **Interoperability** tab and scroll down to the **User account HMAC** section.
- If you don't already have a default project set, set it now.
- Click **create keys** to generate access keys for your account.

</details>

## Prerequisites

Click [here](https://console.cloud.google.com/marketplace/details/mirror-node-public/hedera-mirror-node) to get started; you will need the following details you obtained from the previous [step](one-click-mirror-node-deployment.md#how-to-obtain-google-cloud-platform-requester-pay-information):

- Importer GCP access key
- Importer GCS bucket name
  - Mainnet: `hedera-mainnet-streams`
  - Testnet: `hedera-stable-testnet-streams-2024-02`
- Importer GCP billing project ID
- Importer GCP secret key

Once you deploy your mirror node, you can access the mirror node via the [gRPC API](../../sdks-and-apis/hedera-consensus-service-api.md) or the [REST API](../../sdks-and-apis/rest-api.md).

#### **gRPC API**

Use the following terminal commands:

```bash
GRPC_IP=$(kubectl get service/hedera-mirror-node-1-grpc -n default -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

grpcurl -plaintext "${GRPC_IP}:5600" list
```

#### **REST API**

For the REST API, use these terminal commands:

```bash
REST_IP=$(kubectl get service/hedera-mirror-node-1-rest -n default -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

curl -s "http://${REST_IP}/api/v1/transactions?limit=1"
```

Alternatively, you can also submit a `get` request from your browser:

- From your Kubernetes Engine navigation bar, click on "Workloads."
- Click on "hedera-mirror-node-1-rest"
- Navigate down to "Exposing services" and click on the "Endpoints" link

Example `GET` request:

```bash
http://<yourEndpoint>/api/v1/transactions
```

{% content-ref url="../../sdks-and-apis/rest-api.md" %}
[rest-api.md](../../sdks-and-apis/rest-api.md)
{% endcontent-ref %}
