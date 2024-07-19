---
title: "GitLab Webhook"
order: 2
---


BuildMaster can accept GitLab webhook events to:
 - Create a build in a specific application
 - Run a plan for customizable use cases
 
## Prerequisites

The following prerequisites are required to integrate GitLab Webhooks with BuildMaster:

 - GitLab account with at least 1 project or group
 - BuildMaster v6.1 or higher installed
 - Inbound firewall rule added for the BuildMaster website port on the BuildMaster server

## Configuring GitLab

GitLab supports webhooks at the project or group level. In most cases, the group level is preferable because only a single webhook endpoint needs to be configured. However, configuring a webhook at the group level requires the paid version of GitLab.

To create a webhook, visit the *Settings* > *Integrations* page under the project or group. Set the following options:

 - **URL**: *will be generated in next step*
 - **Secret Token**: *create a secure token and note it for next step*
 - **Trigger**: Push events, be sure to configure the desired branches
 - **SSL verification**: *for security this should be enabled, it requires a valid certificate configuration on the webserver*

## Configuring BuildMaster

In BuildMaster, visit the *Administration* > *Build Triggers & Scheduled Jobs* page and click *Create New Build Trigger* to add the webhook monitor. Creating a GitLab Webhook Monitor includes the following options:
 

- **Run for application**: If a specific application is selected, builds are created for all active releases in that application. If no application is selected, the specified global plan will be executed  instead (see below for an [example](#example-plan))
 - **Secret token**: *Must match the Secret field entered on the GitLab side*
 - **Action**: Whether to create a new build, run a plan, or disable the webhook monitor
 - **Create variables**: Specifies whether `$Branch` or `$CommitHash` should be automatically created as build variables
 - **Plan**: A plan that will be executed if "execute a plan" is the selected action, limited to one application if specified, otherwise a global plan
 - **Name**: A friendly name to describe the webhook endpoint, which also serves as an identifier in the generated payload URL. Only alphanumeric characters, hyphens and underscores are allowed

Once saved, the payload URL (relative to the BuildMaster host) will be visible on the overview page, e.g. `/api/hooks/GitLabHook`.

## Completing GitLab Configuration

Once the webhook receiver is created in BuildMaster, enter that URL relative to the public-facing BuildMaster host as the URL, for example:

`https://buildmaster.inedo.com/api/hooks/GitLabHook`

Once the webhook is saved, BuildMaster is ready to accept GitLab push events.

To test the configuration, simply push a commit or edit a page directly in GitLab. Then use the *Integrations* > *Edit Webhook* page to view the data that was sent to BuildMaster along with the response.

## Custom Plans

If a plan is specified in the BuildMaster webhook configuration, it will run instead of creating builds for the application associated with the webhook. This plan has access to some [default variables](/docs/buildmaster/administration/buildmaster-resource-monitors#variables), but the following are also supported by GitLab:
 

 - `%GitLab.Ref` - the full ref referenced by the commit
 - `%GitLab.HeadCommit.Message` - the message associated with the latest commit
 - `%GitLab.UserName` - the name of the author of the latest commit
 - `%GitLab.Project.Namespace` - the group name of the repository

Custom plans are ideal for several use cases:

 - Creating builds in multiple applications with a single webhook event
 - Ensuring a release exists for a feature branch (e.g. `Ensure-Release(...)`, `Create-Build(...)`)
 - Storing commit IDs, branch names, or commit messages as build configuration variables

### Example Plan

Below is a simple example plan created at the system level that supports creating builds for specific projects in GitLab so that the same webhook URL can be used for all projects:

```
##AH:UseTextMode

Create-Build
(
    Application: $Repository,
    Variables: %(ReleaseCandidate: false)
);
```

A more advanced example can be found in the [GitHub instructions](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control).

## Troubleshooting

Common issues that arise are:

#### The BuildMaster server is inaccessible from the Internet:

In this case, [Repository Monitors](/docs/buildmaster/administration/buildmaster-resource-monitors) may be more suitable because requests are initiated from BuildMaster.

#### No builds are created:

If no custom plan is specified, builds are triggered only for the last *active* release in an application. If a custom plan is specified, then that plan is responsible for enumerating applications and/or releases, and for creating builds.

#### Logs:

The BuildMaster logs for a given trigger (assuming the request reaches BuildMaster at all) can be found on the *Administration* > *Executions* page. Filter by *Mode: Build Trigger* to view them.

The GitLab Integrations page displays all webhook requests, the request body, and responses with an option to resubmit the request.