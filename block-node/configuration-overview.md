# Configuration

The Hiero Block Node repo is composed of multiple components that can be configured using the following options:

## Default Configuration

The default configuration allows users to quickly get up and running without having to configure anything. This provides
ease of use at the trade-off of some insecure default configuration. Most configuration settings have appropriate
defaults and can be left unchanged. It is recommended to browse the properties below and adjust to your needs.

The configuration facility of the Hiero Block Node supports overriding configuration values via environment variables,
with a well-defined transform from property name to environment variable name.

> **Note:** The default configuration should be considered a "production" recommendation, and we may provide separate
> recommended "overrides" for development, test, etc...

## Server

Server configuration options are described at [Server Configuration](block-node/configuration.md).

## Simulator

Simulator configuration options are described at [Simulator Configuration](simulator/configuration.md).
