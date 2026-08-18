# Configure Alloy Telemetry

Grafana Alloy ships Prometheus metrics (remote-write) and Loki logs from the Block Node
cluster to the operator's observability infrastructure.

Telemetry is opt-out for council Tier 1 operators during the initial deployment period.
If you are opting out, skip this page, record the decision with Hashgraph DevOps, and
proceed directly to [Network Validation and Go-Live](./network-validation-go-live.md).

> **Before starting:** Complete [Install the Block Node](./install-block-node.md). Alloy
> requires the Kubernetes cluster to be running.

---

## What you need before starting

Obtain the following from Hashgraph DevOps through the approved coordination channel:

|               Value                |             Placeholder              |                  Notes                  |
|------------------------------------|--------------------------------------|-----------------------------------------|
| Prometheus remote-write URL        | `<PROMETHEUS_REMOTE_WRITE_URL>`      | Non-secret; used on the install command |
| Prometheus remote-write username   | `<PROMETHEUS_REMOTE_WRITE_USERNAME>` | Non-secret                              |
| Prometheus write-only access token | `<PROMETHEUS_REMOTE_WRITE_PASSWORD>` | Secret - deliver via secure channel     |
| Loki remote-write URL              | `<LOKI_REMOTE_WRITE_URL>`            | Non-secret                              |
| Loki remote-write username         | `<LOKI_REMOTE_WRITE_USERNAME>`       | Non-secret                              |
| Loki write-only access token       | `<LOKI_REMOTE_WRITE_PASSWORD>`       | Secret - deliver via secure channel     |

---

## Step 1 - Create the credentials Secret **[OPERATOR]**

Alloy reads credentials from a Kubernetes Secret named `grafana-alloy-secrets` in the
`grafana-alloy` namespace. Create the namespace and Secret before installing Alloy:

```bash
kubectl create namespace grafana-alloy

kubectl create secret generic grafana-alloy-secrets \
  --namespace=grafana-alloy \
  --from-literal=PROMETHEUS_PASSWORD_HASHGRAPH=<PROMETHEUS_REMOTE_WRITE_PASSWORD> \
  --from-literal=LOKI_PASSWORD_HASHGRAPH=<LOKI_REMOTE_WRITE_PASSWORD>
```

The Secret key names follow the pattern `PROMETHEUS_PASSWORD_<REMOTE_NAME>` and
`LOKI_PASSWORD_<REMOTE_NAME>`, where `<REMOTE_NAME>` is the uppercase name used in
`--add-prometheus-remote` and `--add-loki-remote` below. For the Hashgraph remote, the name
is `HASHGRAPH`.

> Destroy any local copy of the credentials file after creating the Secret. The tokens now
> live only in the cluster.
>
> **Security note:** Kubernetes Secrets backed by `etcd` are not encrypted at rest by default
> and may leak sensitive data into swap or temporary files. For production deployments, using
> an External Secrets Operator integrated with a managed vault (HashiCorp Vault, AWS Secrets
> Manager, GCP Secret Manager, or similar) is **strongly recommended**. If you use the default
> `etcd` provider, configure
> [encryption at rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/#ensure-all-secrets-are-encrypted)
> before storing credentials.

You can also populate the Secret using the External Secrets Operator from a vault, Terraform,
or any other mechanism your organization uses - the manual `kubectl` path above is the simplest
path for an initial deployment only.

---

## Step 2 - Install Alloy **[OPERATOR]**

```bash
sudo solo-provisioner alloy cluster install \
  --cluster-name=<BLOCK_NODE_CLUSTER_NAME> \
  --monitor-block-node \
  --profile mainnet \
  --add-prometheus-remote="name=hashgraph,url=<PROMETHEUS_REMOTE_WRITE_URL>,username=<PROMETHEUS_REMOTE_WRITE_USERNAME>,labelProfile=ops" \
  --add-loki-remote="name=hashgraph,url=<LOKI_REMOTE_WRITE_URL>,username=<LOKI_REMOTE_WRITE_USERNAME>,labelProfile=ops"
```

The `labelProfile=ops` option automatically injects standard labels into every metric and
log stream so Hashgraph can attribute and isolate this operator's telemetry:

|      Label       |            Value            |
|------------------|-----------------------------|
| `cluster`        | `<BLOCK_NODE_CLUSTER_NAME>` |
| `environment`    | Set by label profile        |
| `instance_type`  | `lfh`                       |
| `inventory_name` | Operator node label         |
| `ip`             | Block Node public IP        |

---

## Step 3 - Verify Alloy **[COORDINATED]**

Check that the Alloy pod is running:

```bash
kubectl -n grafana-alloy get pods
```

Hashgraph DevOps confirms that metrics and logs are landing in the configured remotes. Do not
proceed to network validation until Hashgraph confirms Alloy is shipping.

---

## Next step

Proceed to [Network Validation and Go-Live](./network-validation-go-live.md).
