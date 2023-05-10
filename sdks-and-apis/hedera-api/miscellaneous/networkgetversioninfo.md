# NetworkGetVersionInfo

Get the deployed versions of Hedera Services and the HAPI proto in semantic version format

#### NetworkGetVersionInfoQuery

| Field    | Type                          | Description                                                                                                                                         |
| -------- | ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `header` | [QueryHeader](queryheader.md) | Standard info sent from client to node, including the signed payment, and what kind of response is requested (cost, state proof, both, or neither). |

#### NetworkGetVersionInfoResponse

Response when the client sends the node NetworkGetVersionInfoQuery

| Field                   | Type                                                 | Description                                                                                                      |
| ----------------------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `header`                | [ResponseHeader](responseheader.md)                  | Standard response from node to client, including the requested fields: cost, or state proof, or both, or neither |
| `hapiProtoVersion`      | [SemanticVersion](../basic-types/semanticversion.md) | The Hedera API (HAPI) protobuf version recognized by the responding node                                         |
| `hederaServicesVersion` | [SemanticVersion](../basic-types/semanticversion.md) | The version of the Hedera Services software deployed on the responding node                                      |
