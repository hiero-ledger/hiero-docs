# One Click Beta Mirror Node Deployment

## Google Cloud Platform Marketplace

Deploy your own mirror node with just a few clicks! The Hedera Mirror Node is open-source software and therefore does not carry with it an associated license or deployment fee. However, users are responsible to pay for the compute resource used, the data retrieved from Google Cloud Storage buckets, and any other Google Cloud Platform services used during this deployment. Click [here](https://console.cloud.google.com/marketplace/details/mirror-node-public/hedera-mirror-node) to get started!

You will need the following information:

* Importer GCP access key
* Importer GCS bucket name
  * Mainnet: hedera-mainnet-streams
  * Testnet: hedera-stable-testnet-streams-2020-08-27
* Importer GCP billing project ID
* Importer GCP secret key

Once you deploy your mirror node, you can access the mirror node via the GRPC API or the REST API.

**GRPC API:**

Terminal commands:

```
GRPC_IP=$(kubectl get service/hedera-mirror-node-1-grpc -n default -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

grpcurl -plaintext "${GRPC_IP}:5600" list
```

**REST API:**

Terminal commands:

```
REST_IP=$(kubectl get service/hedera-mirror-node-1-rest -n default -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

curl -s "http://${REST_IP}/api/v1/transactions?limit=1"
```

You can also submit a `get` request from your browser:

* From your Kubernetes Engine navigation bar, click on "Workloads."
* Click on "hedera-mirror-node-1-rest"
* Navigate down to \*\*"\*\*Exposing services" and click on the "Endpoints" link

Example request:

```
http://<yourEndpoint>/api/v1/transactions
```

{% content-ref url="../docs/mirror-node-api/rest-api.md" %}
[rest-api.md](../docs/mirror-node-api/rest-api.md)
{% endcontent-ref %}
