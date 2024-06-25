---
description: Common questions for the current Hedera's permissioned Mainnet consensus nodes
---

# FAQ

## What security protocols and key sizes are used by hashgraph?

The nodes use TLS 1.2 DH RSA 3k keys and SHA384 to secure communications amongst nodes. Our goal is to satisfy CNSA suite1 as specified by the US government. TLS 1.2 uses ephemeral AES 256 for perfect forward secrecy. Different keys are used for the TLS key exchange and a different one for signatures. Clients use ED25519 to sign transactions.

## Does a consensus node support bonding or splitting ingress and egress traffic?

Hashgraph does not support bonding or splitting of ingress and egress traffic.

## Does the consensus node need access to our internal network?

The Hedera consensus node does not need access to any internal resources and must be separated from the rest of the corporate network, ideally in its own DMZ (Demilitarized Zone).

## Does the consensus node need to be backed up?

Application specific backups are not required. Since the Hedera network continue to process transactions while the failed node is down, restored backups will be out of date by the time they are recovered. Redundancy comes from the other nodes and the recovered node will be resynchronized by the hashgraph software.

It is expected that normal backup procedures are in place for the operating system level to allow for rapid and consistent recovery for disaster situations included hardware failures and similar situations.

## What are the consensus node SLA and operational requirements?

The LLC agreement specifies that "while a desired initial target should be at least 99.5% availability, inclusive of scheduled technical/operational maintenance windows".

External monitoring will be available from Hedera for notification of failure.
