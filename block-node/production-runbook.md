# Production Runbook

{% hint style="info" %}
For node operators onboarded by the Hiero operator team. If you are setting up a Block Node independently, use the [Deployment](deployment.md) and [Configuration](configuration.md) guides instead.
{% endhint %}

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody>
<tr><td><strong>Prerequisites</strong></td><td>Infrastructure, access, and software requirements before beginning a production Block Node deployment.</td><td><a href="block-node/block-node/operations/production-guide/prerequisites.md">prerequisites.md</a></td></tr>
<tr><td><strong>Install the Block Node</strong></td><td>Install and start the Block Node using Helm, configure plugins, and verify the initial deployment.</td><td><a href="block-node/block-node/operations/production-guide/install-block-node.md">install-block-node.md</a></td></tr>
<tr><td><strong>Configure Alloy Telemetry</strong></td><td>Set up Grafana Alloy to scrape Block Node metrics and forward them to your observability stack.</td><td><a href="block-node/block-node/operations/production-guide/configure-alloy-telemetry.md">configure-alloy-telemetry.md</a></td></tr>
<tr><td><strong>Network Validation and Go-Live</strong></td><td>Validate Block Node connectivity, stream health, and block verification before connecting to the live network.</td><td><a href="block-node/block-node/operations/production-guide/network-validation-go-live.md">network-validation-go-live.md</a></td></tr>
<tr><td><strong>Steady State Operations</strong></td><td>Day-to-day operational procedures: monitoring dashboards, log review, storage management, and upgrade cadence.</td><td><a href="block-node/block-node/operations/production-guide/steady-state-operations.md">steady-state-operations.md</a></td></tr>
<tr><td><strong>Disaster Recovery</strong></td><td>Procedures for recovering from disk failure, data corruption, or extended downtime.</td><td><a href="block-node/block-node/operations/production-guide/disaster-recovery.md">disaster-recovery.md</a></td></tr>
<tr><td><strong>Getting Help</strong></td><td>Support channels, issue reporting, and escalation paths for production Block Node operators.</td><td><a href="block-node/block-node/operations/production-guide/getting-help.md">getting-help.md</a></td></tr>
</tbody></table>
