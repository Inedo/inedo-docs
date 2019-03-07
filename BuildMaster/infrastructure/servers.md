---
title: Servers
subtitle: Servers
sequence: 10
keywords: buildmaster, servers, infrastructure, environments
show-headings-in-nav: true
---
BuildMaster deploys your application releases to the servers that you configure.

::: attention {.analogy}
A server can be physical (bare metal), virtual, or even nonexistent (i.e. one that you will provision later using [Otter](/otter)).
:::

### Adding Servers to BuildMaster {#adding-servers data-title="Adding Servers to BuildMaster"}

You can add a server using the web-based user interface (Servers > Add Server), or programmatically with the [infrastructure API](/support/documentation/buildmaster/reference/api/infrastructure).

BuildMaster communicates with servers using the [Inedo Agent](/support/documentation/various/inedo-agent/the-agent) (for Windows) or <a [SSH/SFTP](https://www.ssh.com/ssh/sftpt) (for Windows and Linux).

## Deploying to Servers {#deploying-to-servers data-title="Deploying to Servers"}

In addition to using [Server Roles](/support/documentation/buildmaster/infrastructure/server-roles), there are two ways to deploy to a server:

### Pipeline Stage Target {#pipeline-target data-title="Pipeline Stage Target"}

You can specify a list of servers as a stage target in a [pipeline stage](/support/documentation/buildmaster/core-concepts/pipelines#Pipeline-stages). When the build is deployed to that stage, the stage target's deployment plan will be run against all those servers.

### Servers and OtterScript {#servers-otter data-title="Servers and OtterScript"}

You can deploy to a server using a [general block](/support/documentation/buildmaster/execution-engine/statements-and-blocks/general-blocks)

```
for server prod-hdars-sv1
{
    Deploy-Artifact hdars-api-service
    (
        To: C:\HDars\App\api-service
    );
}
```

### Server vs Server Roles {#server-server-roles data-title="Server vs Server Roles"}

Deploying to server roles can be helpful when you don't want to explicitly specify server names. For example, in a multi-tier application that's designed to run across multiple servers.

However, if your application has always been deployed to a specific server, and will be deployed to that specific server for the foreseeable future, there's not much value in creating a specific role for it. In fact, it may create confusion, as other users may be unfamiliar with the concept of roles.

## Resource Pools {#resource-pools data-title="Resource Pools"}

A resource pool is a set of servers that are used as a single, load-balanced resource. You may acquire an unused server from a resource pool, and then release it back into the pool once the needed tasks have been performed.

Visit the [Resource Pools documentation of the Inedo Execution Engine](/support/documentation/various/execution-engine/resource-pools)  for more information on how to configure a resource pool.
