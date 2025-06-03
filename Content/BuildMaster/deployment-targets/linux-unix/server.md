---
title: "Deploying to a Linux or Unix Server Using the SSH Agent"
order: 2
---

BuildMaster can be deployed to physical servers or virtual machines running Linux or Unix. BuildMaster can use the SSH agent to communicate with and orchestrate tasks on these platforms without requiring an agent to be installed on the target server.

## Adding Servers to BuildMaster 

In order to reference a server in a build or deployment plan (or pipeline), it must be configured in BuildMaster first. To add a server to BuildMaster, visit the [Servers administration documentation](/docs/buildmaster/administration-agents-and-infrastructure/buildmaster-servers) for more information.

## Targeting Servers for Deployment 

There are two primary ways to deploy an application to a Linux or Unix server:

### Servers and OtterScript 

You can target a server in a build or deployment plan directly by using a [general block](/docs/executionengine/otterscript/statements-and-blocks/general), using the OtterScript `for server <server-name>` statement. This will execute all OtterScript operations in the context of the specified server. In other words, it's equivalent to using SSH to run commands remotely on the server. 

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

### Pipeline Stage Targeting 

The recommended method when deploying to server(s) is to define them in the [pipeline stage target](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines#pipeline-stages). When this is done, the associated build or deployment plan for that stage target is  effectively wrapped in a `for server` block, as in the example above, (or multiple blocks if [server roles](/docs/buildmaster/administration-agents-and-infrastructure/server-roles) are targeted instead); this automatically runs contained operations in the context of the specified servers. Stage targeting offers the following benefits over explicit `for server` blocks:

 - simplifies plan logic
 - ability to reuse plans across any server
 - easier to scale by targeting a server role and adding servers to role