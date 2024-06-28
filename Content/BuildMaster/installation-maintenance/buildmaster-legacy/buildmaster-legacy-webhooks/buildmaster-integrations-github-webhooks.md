---
title: "GitHub Webhook"
order: 1
---


BuildMaster can accept GitHub webhook events to:

 - Create a build in a specific application
 - Run a plan for more customizable use cases

## Pre-requisites

The following prerequisites are required to integrate GitHub Webhooks with BuildMaster:

 - GitHub account with at least 1 repository or organization
 - BuildMaster v6.1 or higher installed
 - Incoming firewall rule added for the BuildMaster website port on the BuildMaster server

## Configuring GitHub

GitHub supports webhooks at the repository or organization level. In most cases, the organization level is preferable because only a single webhook endpoint needs to be configured.

To create a webhook, visit the *Settings* > *Webhooks* section of the repository or organization. Set the following options:


 - **Payload URL**: *will be generated in next step*
 - **Content type**: application/JSON
 - **Secret**: *create a secure token and note it for next step*
 - **SSL verification**: *for security this should be enabled, requires a valid certificate configuration on the webserver*
 - **Events**: Just the push event
 - **Active**: checked

## Configuring BuildMaster

In BuildMaster, visit the *Administration* > *Build Triggers & Scheduled Jobs* page and click *Create New Build Trigger* to add the Webhook Monitor. Creating a GitHub Webhook Monitor includes the following options:

- **Run for application**: If a specific application is selected, builds are created for all active releases in that application. If no application is selected, the specified global plan is executed instead (see below for an [example](#example-plan))
 - **Secret token**: *This must match the Secret field entered on the GitHub side*
 - **Action**: Whether to create a new build, run a plan, or disable the webhook monitor
 - **Create variables**: Specifies whether `$Branch` or `$CommitHash` will automatically be created as build variables
 - **Plan**: A plan that is executed when "execute a plan" is the selected action, limited to one application if specified, otherwise a global plan
 - **Name**: A friendly name to describe the webhook endpoint, which also serves as an identifier in the generated payload URL. Only alphanumeric characters, hyphens and underscores are allowed

Once saved, the payload URL (relative to the BuildMaster host) will be visible on the overview page, e.g. `/api/hooks/GitHubHook`.

## Completing GitHub Configuration

Once the webhook monitor is created in BuildMaster, enter that URL relative to the public-facing BuildMaster host as the payload URL, for example:

`https://buildmaster.inedo.com/api/hooks/GitHubHook`

Once the webhook is saved, BuildMaster is ready to accept GitHub push events. 

To test the configuration, simply push a commit or edit a page directly on GitHub. Then use the *Settings* > *Webhooks* > *Edit* page to view the data that was sent to BuildMaster, along with the response.

## Custom Plans

If a plan is specified in the BuildMaster webhook configuration, it is executed instead of creating builds for the application associated with the webhook. This plan has access to some [default variables](/docs/buildmaster/administration/buildmaster-resource-monitors#variables), but GitHub also supports:

 - `%GitHub.Ref` - the full ref referenced by the commit
 - `%GitHub.HeadCommit.Message` - the message associated with the commit
 - `%GitHub.Repository.FullName` - the full name of the repository including the organization
 - `%GitHub.Compare` - the URL that shows the diff against the previous commit

Custom plans are ideal for several use cases:
 
 - Creating builds in multiple applications with a single webhook event
 - Ensuring that a release exists for a feature branch (e.g. `Ensure-Release(...)`, `Create-Build(...)`)
 - Storing commit IDs, branch names, or commit messages as build configuration variables

### Example Plan

The following is an example plan created at the system level that supports the creation of builds for an entire organization, specifically the plan used to trigger builds for all active releases of extensions in the [Inedo organization](https://github.com/inedo) on GitHub:

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

#### The BuildMaster server is inaccessible from the Internet:

In this case, [Repository Monitors](/docs/buildmaster/administration/buildmaster-resource-monitors) may be more suitable because requests are initiated from BuildMaster.

#### No builds are created:

If no custom plan is specified, builds are triggered only for the last *active* release in an application. If a custom plan is specified, then that plan is responsible for enumerating applications and/or releases, and for creating builds.

#### Logs:

The BuildMaster logs for a given trigger (assuming the request reaches BuildMaster at all) can be found on the *Administration* > *Executions* page. Filter by *Mode: Build Trigger* to view them.

The GitHub Webhooks page displays all webhook requests, request text, and responses, with an option to resubmit the request.