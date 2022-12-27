---
description: >-
  Notable additions and changes to Hedera software and services code requiring
  further explanation.
---

# Changelog

## ⚪ Account Balance CSV Removal \[July 2021]

**IMPACT**: Account balance CSV file

{% hint style="info" %}
**Version:** Hedera Services v0.15.1\
**Mainnet:** July 1, 2021\
**Testnet:** June 17, 2021
{% endhint %}

A new account balance file format was introduced in the Hedera Services v0.12.0 release. An [AccountBalanceFile.proto](https://github.com/hashgraph/hedera-protobufs/blob/main/streams/account\_balance\_file.proto) has been added to the hedera-protobufs repository. The new file along with its signature file is compressed using gzip compression. Both the CSV file format and proto file format will exist in the same path and with the same name apart from the file extension being pb.gz and pb\_sig.gz.

Account Balance Proto File Name Example:

* 2021-03-02T23\_59\_54.477514Z\_Balances.csv
* 2021-03-02T23\_59\_54.477514Z\_Balances.csv\_sig
* 2021-03-02T23\_59\_54.477514Z\_Balances.pb.gz
* 2021-03-02T23\_59\_54.477514Z\_Balances.pb\_sig.gz

The CSV account balance format is deprecated and will be removed in the Hedera Services 0.15.0 release. Please update to the new account balance file formats and use the AccountBalanceFile protobuf to parse the files.

Please reference the following issues:

* [Hedera Services Issue #852](https://github.com/hashgraph/hedera-services/issues/852)
* [Hedera Services Issue #1012](https://github.com/hashgraph/hedera-services/issues/1012)

## ⚪ Hedera SDK v1 Deprecation \[October 2021]

**IMPACT:** Hedera v1 SDKs

{% hint style="info" %}
**DEPRECATION DATE:** May 2021\
**REMOVAL**: October 2021
{% endhint %}

| SDK                       | Version |
| ------------------------- | ------- |
| **Hedera Java SDK**       | 1.x     |
| **Hedera JavaScript SDK** | 1.x     |

Hedera v1 SDKs for Java and JavaScript will no longer be supported after October 2021. Users are advised to update to the latest 2.x version of the SDKs as soon as possible. Please reference this [blog](https://hedera.com/blog/deprecation-timeline-for-v1-of-the-open-source-hedera-sdk-for-java-javascript) for additional information.

## ⚪ Address Book Protobuf Deprecations \[December 2021]

**IMPACT**: Parsing address book files 0.0.101 and 0.0.102

{% hint style="info" %}
Deprecated fields will no longer be populated in the 0.20.0 Hedera Services release.
{% endhint %}

A new `ServiceEndpoint` protobuf message was introduced in the Hedera Services codebase v0.13.0 release.

| Type     | Protobuf Message                                                                                             | Description                                                                                                                                                      |
| -------- | ------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| New      | [ServiceEndpoint](https://github.com/hashgraph/hedera-protobufs/blob/develop/services/BasicTypes.proto#L344) | A node's service IP addresses and ports                                                                                                                          |
| Modified | [NodeAddress](https://github.com/hashgraph/hedera-protobufs/blob/develop/services/BasicTypes.proto#L358)     | <p>Added field:</p><p>ServiceEndPoint</p><p>description</p><p>stake</p><p>Deprecated fields:<br><em>ipAddress</em></p><p><em>portNo</em></p><p><em>memo</em></p> |

The deprecated fields will remain in the 0.13.0 release and are not expected to be removed in a future release at this time to ensure historical address book files can be parsed.

After the six-month depreciation period (v0.20.0) the deprecated fields will no longer be populated and the new protobuf will need to be used to parse new address book files.

Please reference the following issues:

* [Hedera Services Issue #750](https://github.com/hashgraph/hedera-services/issues/750#issuecomment-815916066)
* [Hedera Protobuf Issue #21](https://github.com/hashgraph/hedera-protobufs/issues/21)
* [Hedera Protobuf Issue #25](https://github.com/hashgraph/hedera-protobufs/issues/25)
