# Deployment with Selected Plugins

## Table of Contents

1. [Purpose](#purpose)
2. [Goals](#goals)
3. [Design](#design)
4. [Acceptance Tests](#acceptance-tests)

## Purpose

We need the ability to deliver image with minimum required plugins. We also need to easily support
adding or removing plugins as part of a deployment process, including adding
future community or user developed plugins.

## Goals

1. Build with all Hiero-defined plugins in the `hiero-ledger/block-node` repo for E2E testing.
2. Build with no plugins whatsoever for use by Helm chart deployments that add plugins dynamically
   based on chart configuration.
3. Modify the build to publish core plugin modules to Maven Central
   as individual libraries so that they can be downloaded individually during Helm chart deployments.
4. Modify the build to publish the "bare" image with no plugins.

## Design

Create a plugin-free OCI image with a well-defined extension folder
where plugin jars can be added during deployment.
Define helm chart values files for the two current deployments and two additional testing-only options
(Minimal, RFH, LFH, all plugins testing)

We cannot reasonably create OCI images for every possible combination of plugins,
and we want to support third-party plugins or private plugins.
To do that we will add a volume extension point in the published OCI image
where an operator can place additional plugins to be loaded.
The cleanest way to support that at deployment would be to enable the Helm chart
to add arbitrary jars to that extension location based on value files, so an operator
just lists the required maven dependencies in their values file and those plugins are loaded
into that deployment by the init container and script defined in the Helm chart.

To achieve that we will have Minimal, RFH, LFH and all-plugins overrides in values-overrides folder.
Each override file will specify the required plugins for that deployment. An init container in the
Helm chart downloads the specified plugins from Maven Central and places them in a shared volume
that is mounted to the application container's plugin directory.

### Deployment Profiles

|            Profile            |        Purpose        |                   Description                    |
|-------------------------------|-----------------------|--------------------------------------------------|
| **all-plugins**               | Development & Testing | Full functionality with all available plugins    |
| **minimal**                   | Development & Testing | Minimum viable node without archival or backfill |
| **lfh** (Local File History)  | Production            | Stores all blocks locally on persistent volumes  |
| **rfh** (Remote File History) | Production            | Stores blocks in S3; no local historic storage   |

### Plugin Dependencies

- **Required:** `facility-messaging` - Core messaging; application will not start without it
- **Recommended:** `health` - Required for Kubernetes probes
- **Recommended:** `server-status` - Metrics and status endpoints

### Custom Configurations

Operators can create custom plugin combinations by specifying a comma-separated list of plugin names
in their values file:

```yaml
plugins:
  enabled: true
  names: "facility-messaging,health,server-status,block-access-service,verification"
```

See the [Helm chart README](../../charts/block-node-server/README.md#plugin-configuration) for
complete plugin configuration documentation.

## Acceptance Tests

### Verify e2e tests are passing after the changes

1. Build and publish the minimal OCI image with no plugins.
2. Run the existing e2e tests using the "all testing plugins" Docker Compose definition.
3. Verify that all e2e tests pass successfully.

### Verify that the minimal image can be deployed with only the required plugins

1. Build and publish the minimal OCI image with no plugins.
2. Deploy the minimal image using the Helm chart with the minimum set of plugins (e.g. Messaging, Health, and Status).
3. Verify that the deployment is successful and the application starts correctly.

### Verify that additional plugins can be added to the minimal image during deployment

1. Prepare a set of additional plugin jars to be added.
   * Messaging
   * Health
   * Status
   * Stream Publisher
   * Verification
   * Stream Subscriber
   * Files-Recent
2. Deploy the "bare" image using the Helm chart, specifying the additional plugins in the values file.
3. Verify that the deployment is successful and the application starts correctly with the additional plugins loaded.
4. Verify that all additional plugins are functioning as expected.
