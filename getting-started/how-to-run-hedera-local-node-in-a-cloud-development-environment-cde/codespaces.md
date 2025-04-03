# Codespaces

Codespaces is a cloud development environment (CDE) that's hosted in the cloud. You can customize your project for GitHub Codespaces by committing configuration files to your repository (often known as Configuration-as-Code), which creates a repeatable codespaces configuration for all users of your project. [GitHub Codespaces overview](https://docs.github.com/en/codespaces/overview)

***

## Prerequisites

* Review first the [Quickstart for GitHub Codespaces](https://docs.github.com/en/codespaces/getting-started/quickstart) guide.
* Install VS Code Desktop application.
* In [Editor preference](https://github.com/settings/codespaces) change your client to `Visual Studio Code` (Should not be `Visual Studio Code for the Web`)

***

## Configure Dev Container

To configure t he dev container, open the [Hedela Local Node repo](https://github.com/hashgraph/hedera-local-node) and click on the `Code`->`Codespaces`->`...`-> `Configure dev container`.

<figure><img src="../../.gitbook/assets/codespace-config-dev-container.png" alt="" width="563"><figcaption></figcaption></figure>

This will open the dev container configuration file where you can customize your configuration like the CPUs and memory.

<figure><img src="../../.gitbook/assets/codespace-config-file.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
**Note**: If you make changes to your config file, commit and push your changes before running local node, to ensure the project starts with the right configuration.
{% endhint %}

## Creating and Running Your Codespace

Open the [Hedela Local Node repo](https://github.com/hashgraph/hedera-local-node) and click on the `Code`->`Codespaces`->`...`-> `New with options...` button and choose the appropriate settings:

<figure><img src="../../.gitbook/assets/local-node-codespaces (1).jpeg" alt="" width="563"><figcaption></figcaption></figure>

Once your codespace is created, the template repository will be automatically cloned into it. Your codespace is all set up and have the local node running!

<figure><img src="../../.gitbook/assets/local-node-codespace-config.png" alt="" width="563"><figcaption></figcaption></figure>

***

## Conclusion and Additional Resources

Congrats on successfully setting up your Codespace and running a Hiero Local Node!

**➡** [**Hiero Local Node Repository**](https://github.com/hashgraph/hedera-local-node#readme)

**➡** [**Quickstart for GitHub Codespaces**](https://docs.github.com/en/codespaces/getting-started/quickstart)

**➡** [**Adding Dev Container Config to Repo**](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration)
