# How to Run Hedera Local Node in a Cloud Development Environment (CDE)

The [**Hedera Local Node**](https://github.com/hashgraph/hedera-local-node) project enables developers to establish their own local network for development and testing. The local network comprises the consensus node, mirror node, [JSON-RPC relay](https://github.com/hashgraph/hedera-json-rpc-relay#readme), and other Hedera services be set up without Docker and draining your computer’s resources by using .Cloud Development Environments (CDEs). CDEs allows developers to work from any device without the need to maintain static and brittle local development environments.

## **Available Services**

The Hedera local node comes with various services, each serving different functions, and accessible locally. These are the endpoints for each service:

| Type                                               | Endpoint                                                                                                         |
| -------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| Consensus Node Endpoint                            | [http://localhost:50211/](http://localhost:50211/)                               |
| Mirror Node GRPC Endpoint                          | [http://localhost:5600/](http://localhost:5600/)                                 |
| Mirror Node REST API Endpoint                      | [http://localhost:5551/](http://localhost:5551/)                                 |
| JSON RPC Relay Endpoint                            | [http://localhost:7546/](http://localhost:7546/)                                 |
| JSON RPC Relay Websocket Endpoint                  | [http://localhost:8546/](http://localhost:8546/)                                 |
| Mirror Node Explorer (HashScan) | [http://localhost:8080/devnet/dashboard](http://localhost:8080/devnet/dashboard) |
| Grafana UI                                         | [http://localhost:3000/](http://localhost:3000/)                                 |
| Prometheus UI                                      | [http://localhost:9090/](http://localhost:9090/)                                 |

You may access these services on `localhost`, and these endpoints are set up to be accessed from your own computer as if they were running locally. Since Gitpod and Codespaces are cloud-based development environments, “localhost” here refers to a virtual environment on cloud servers that you're accessing through your browser. Gitpod and Codespaces redirects these local addresses to your cloud workspace, making it feel as though you're working on a local setup.
