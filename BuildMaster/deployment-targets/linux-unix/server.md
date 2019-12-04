---
title: Deploying to a Linux or Unix Server
subtitle: Deploying to a Linux or Unix Server Using the SSH Agent
sequence: 200
keywords: buildmaster, linux, ssh
show-headings-in-nav: true
---

BuildMaster can deploy to physical servers or virtual machines running Linux or Unix. BuildMaster can communicate with and orchestrate tasks on these platforms using the SSH agent, and does not require an agent to be installed on the target server.

## Adding Servers to BuildMaster {#adding data-title="Adding Servers to BuildMaster"}

In order to reference a server in a build or deployment plan (or pipeline), it must be configured in BuildMaster first. To add a server to BuildMaster, visit the [Servers administration documentation](/docs/buildmaster/administration/agents-and-infrastructure/servers) for more information.

## Targeting Servers for Deployment {#targeting data-title="Targeting Servers"}

There are two primary ways to deploy an application to a Linux or Unix server:

### Servers and OtterScript {#otterscript}

You can target a server in a build or deployment plan directrly using a [general block](/docs/executionengine/otterscript/statements-and-blocks/general-blocks), using the OtterScript `for server <server-name>` statement. This will execute all OtterScript operations in the context of the specified server. In other words, it's equivalent to using SSH to run commands remotely on the server. 

The following command deploys an example website artifact on an Ubuntu server:

```
for server us-central-ubuntu-web-06
{
    Deploy-Artifact hdars-web
    (
        To: /var/www/html
    );
}
```

### Pipeline Stage Targeting {#pipeline-target}

The recommended method when deploying to server(s) is to define them in the [pipeline stage target](/docs/buildmaster/verification/pipelines#pipeline-stages). When this is done, the associated build or deployment plan for that stage target are effectively wrapped in a `for server` block like the above example (or multiple blocks if [server roles](/docs/buildmaster/administration/agents-and-infrastructure/server-roles) are targeted instead), automatically running contained operations in the context of the specified servers. Stage targeting offers the following benefits over explicit `for server` blocks:

{.docs}
 - simplifies plan logic
 - ability to re-use plans across any server
 - easier to scale by targeting a server role and adding servers to role
