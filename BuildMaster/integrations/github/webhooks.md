---
title: GitHub Webhooks
subtitle: Configuring GitHub Webhook Monitors
show-headings-in-nav: true
sequence: 300
keywords: buildmaster, builds, github, webhook-monitors
---

BuildMaster can accept GitHub webhook events in order to:
 
{.docs}
 - Create a build in a specific application
 - Execute a plan for more customizable use-cases

## Pre-requisites

The following pre-requisites are required to integrate GitHub webhooks with BuildMaster:

{.docs}
 - GitHub account with at least 1 repository or organization
 - BuildMaster v6.1 or later installed
 - Inbound firewall rule added for BuildMaster's website port on the BuildMaster server

## Configuring GitHub

GitHub supports webhooks at the repository or organization level. In most cases, the organization level is preferable because only a single webhook endpoint needs to be configured.

To create a webhook, visit the *Settings* > *Webhooks* section of the repository or organization. Set the following options:

{.docs}
 - **Payload URL**: *will be generated in next step*
 - **Content type**: application/json
 - **Secret**: *create a secure token and note it for next step*
 - **SSL verification**: *for security this should be enabled, requires a valid certificate configuration on the webserver*
 - **Events**: Just the push event
 - **Active**: checked

## Configuring BuildMaster

In BuildMaster, visit the *Administration* > *Build Triggers & Scheduled Jobs* page and click *Create New Build Trigger* to add the webhook monitor. Creating a GitHub Webhook Monitor includes the following options:
 
{.docs}
 - **Run for application**: if a specific application is selected, builds will be created for any active releases in that application. If no application is selected, the specified global plan is executed instead (see below for an [example](#example-plan))
 - **Secret token**: *this must match the Secret field entered on the GitHub side*
 - **Action**: whether to create a new build, execute a plan, or disable the webhook monitor
 - **Create variables**: specifies whether `$Branch` or `$CommitHash` will automatically be created as build variables
 - **Plan**: a plan that will be executed if "execute a plan" is the selected action, scoped to an application if specified, otherwise a global plan
 - **Name**: a friendly name describing the webhook endpoint that also functions as the identifier in the generated payload URL. Only alphanumeric characters, dashes, and underscores are permitted

Once saved, the payload URL, relative to the BuildMaster host, will be visible on the overview page, e.g. `/api/hooks/GitHubHook`.

## Completing GitHub Configuration

Once the webhook monitor is created in BuildMaster, enter that URL relative to the publicly-accessible BuildMaster host as the payload URL, for example:

`https://buildmaster.inedo.com/api/hooks/GitHubHook`

Once the webhook is saved, BuildMaster is ready to accept GitHub push events. 

To test the configuration, simply push a commit or edit a page directly on GitHub, then use the *Settings* > *Webhooks* > *Edit* page to view the data that was sent to BuildMaster, along with the response.

## Custom Plans

If a plan is specified in the BuildMaster webhook configuration, it will execute instead of creating builds for the webhook's associated application. This plan has access to some [default variables](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors/repository-monitors#variables), but GitHub also supports:

{.docs}
 - `%GitHub.Ref` - the full ref referenced by the commit
 - `%GitHub.HeadCommit.Message` - the message associated with the commit
 - `%GitHub.Repository.FullName` - the full name of the repository including the organization
 - `%GitHub.Compare` - the URL that shows the diff against the previous commit

Custom plans are ideal for several use-cases:
 
 - Creating builds in multiple applications with a single webhook event
 - Ensuring a release exists for a feature branch (e.g. `Ensure-Release(...)`, `Create-Build(...)`)
 - Storing commit IDs, branch names, or commit messages as build configuration variables

### Example Plan

The following is an example plan created at the system level that supports creating builds for an entire organization, specifically the one used to trigger builds for all active releases of extensions in the [Inedo organization](https://github.com/inedo) on GitHub:

```
##AH:UseTextMode

Log-Debug "Webhook received for $Repository repository (Branch=$Branch)";

if $Branch == master && $MatchesRegex($Repository, ^inedox-.`$*)
{
    set $appName = $Substring($Repository, 7);
    Log-Debug "Looking for $appName application...";
    
    if $ApplicationExists($appName)
    {
        Log-Debug "$appName application found. Creating build...";
        
        Create-Build
        (
            Application: $appName
        );
    }
}
```

## Troubleshooting

Common issues that arise are:

##### The BuildMaster server is inaccessible from the internet:

In this case, [Repository Monitors](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors/repository-monitors) may be more suitable because requests are initiated from BuildMaster.

##### No builds are created:

When no custom plan is specified, builds are only triggered for the latest *active* releass in an application. If a custom plan is specified, then that plan is responsible for enumerating applications and/or releases, and also creating builds.

##### Logs:

The BuildMaster logs for a specific trigger (assuming the request ever reaches BuildMaster) are found on the *Administration* > *Executions* page. Filter by *Mode: Build Trigger* to view them.

The GitHub Webhooks page shows all webhook requests, request body, and responses, with an option to resend the request.
