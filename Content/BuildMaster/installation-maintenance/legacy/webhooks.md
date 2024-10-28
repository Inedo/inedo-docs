---
title: "Webhook Monitors"
order: 2
url-slug: "buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-webhooks"
---

:::(Error) (Webhook Monitors are Deprecated)
With the introduction of expanded [Git & Source Control](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control) features in BuildMaster 2022, this feature is redundant and will be removed in a future version of BuildMaster; see [Legacy (Deprecated) Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy) to learn more.
:::

BuildMaster can receive custom webhook events triggered by GitHub and GitLab. Once a webhook event is triggered, BuildMaster can use the received payload to create a new build in one or more applications or perform other tasks as described in OtterScript.

## Configuring a Webhook Monitor

While webhook monitors depend on the platform, they are configured in BuildMaster in a similar way. A webhook monitor can be created at the system level from the *Administration* > *Build Triggers & Scheduled Jobs* page, or from the *Builds* > *Triggers* tab in the context of an application.

The general configuration for a webhook monitor includes:

 - Some type of secret key/token to verify the authenticity of the sender
 - Whether to create a build or run a custom plan
 - Whether to automatically create build variables to capture the commit ID and branch name

Once these options are configured, a URL (relative to the BuildMaster host) is provided that is required by the third-party system.

## GitHub Webhook Monitor

In order for BuildMaster to receive webhooks from GitHub, a GitHub Webhook Monitor must first be created in BuildMaster, followed by adding a webhook in GitHub itself.
See the [archived GitHub Webhooks documentation](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/BuildMaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-webhooks/buildmaster-integrations-github-webhooks.md) for step-by-step instructions.

## GitLab Webhook Monitor
In order for BuildMaster to receive webhooks from GitLab, a GitLab Webhook Monitor must first be created in BuildMaster, followed by adding a webhook in GitLab itself. 
See the [archived GitLab Webhooks documentation](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/BuildMaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-webhooks/buildmaster-integrations-gitlab-webhooks.md) for step-by-step instructions.