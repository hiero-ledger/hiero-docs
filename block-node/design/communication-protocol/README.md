# Protocol Documents

This folder contains documents describing the expected protocol for various
APIs provided by the Block Node and related systems.
Each protocol document should describe a single API call and the expected
behavior of both sides of that API call, including common error conditions.

## Contents

| Document                                             |               API call | Description                                                                                                                                         |
|:-----------------------------------------------------|-----------------------:|:----------------------------------------------------------------------------------------------------------------------------------------------------|
| [publishBlockStream.md](publish-block-stream.md)     |   `publishBlockStream` | The communication between a publisher and a Block Node when publishing a Block Stream from an authoritative source such as a Consensus Node.        |
| [subscribeBlockStream.md](subscribe-block-stream.md) | `subscribeBlockStream` | The communication between a Block Node and a subscriber (such as a Mirror Node) when streaming blocks from the Block Node to a downstream consumer. |
