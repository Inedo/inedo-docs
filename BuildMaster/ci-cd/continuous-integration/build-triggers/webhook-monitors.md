---
title: Webhook Monitors
show-headings-in-nav: true
sequence: 200
keywords: buildmaster, releases, builds, webhook-monitors
---

BuildMaster can receive custom webhook events fired by GitHub and GitLab. Once a webhook event is triggered, BuildMaster can use the payload data it receives to create a new build in one or more applications, or perform other tasks as described in OtterScript.

## Configuring a Webhook Monitor {#configuring data-title="Configuring a Webhook"}

While webhook monitors are dependent on the platform, they are configured within BuildMaster in a similar manner. A webhook monitor may be created at the system level from the *Administration* > *Build Triggers & Scheduled Jobs* page, or from the *Builds* > *Triggers* tab in the context of an application.

The general configuration for a webhook monitor includes:

{.docs}
 - Some kind of secret key/token used to verify the authenticity of the sender
 - Whether to create a build or execute a custom plan
 - Whether to automatically create build variables to capture the commit ID and branch name

Once these options are configured, a URL (relative to the BuildMaster host) will be provided that will be required by the third-party system.

## GitHub Webhook Monitor {#github data-title="GitHub Hooks"}

In order for BuildMaster to receive webhooks from GitHub, a GitHub Webhook Monitor must be created in BuildMaster first, followed by adding a webhook in GitHub itself. See the [GitHub Webhooks](github-hooks) documentation for step-by-step instructions.

## GitLab Webhook Monitor {#gitlab data-title="GitLab Hooks"}

In order for BuildMaster to receive webhooks from GitLab, a GitLab Webhook Monitor must be created in BuildMaster first, followed by adding a webhook in GitLab itself. See the [GitLab Webhooks](gitlab-hooks) documentation for step-by-step instructions.

