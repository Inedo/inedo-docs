---
title: Server Roles
keywords: otter,server-roles
sequence: 100
show-headings-in-nav: true
---

Roles are used to define sets of configurations that can be assigned to any number of servers.

For example, you may have an `iis-server` role that ensures IIS is configured in a particular way, as well as an `hdar-api-host` role that ensures a particular web application is configured properly. Both roles can be applied to the same server so that `hdar-api-host` is configured on an IIS server.

## Defining Server Roles {#defining data-title="Defining Roles"}

You can define server roles using the web-based interface (Roles > Create Roles), or programmatically with the [Infrastructure API](/support/documentation/otter/reference/api/infrastructure).


![Creating Roles in Otter](/resources/documentation/otter/2/create-roles.png){.screenshot}

## Configuration Drift {#configuration-drift data-title="Configuration Drift"}

Just as an individual server can become [Drifted](../core-concepts/servers#drift), so can server roles. If any of the servers in the role have configuration that differ from the desired configurations, then the entire server role is considered drifted.

Server role drift is remediated in the same manner as configuration drift: either automatically, or by creating a [Configuration job](../core-concepts/jobs#configuration) that targets the role and, optionally, an [Environment](/support/documentation/otter/modeling-infrastructure/environments).

When remediating server role drift, only that server role's configuration plan is executed.

### Example: Multiple Server Roles {#multiple}

Let's say you have a complex, multi-tier system called **HDars** that's designed to scale and run across multiple servers.

{.docs}
- `HDars-Cloud` could describe the configuration of the middle-tier, cloud-data processing components
- `HDars-Web` might describe how HDars is configured in IIS for end users to use
- `IIS-server` would ensure that IIS is installed and configured in a manner appropriate for your organization
- `HDars-database` would configure the database and make sure it's running and configured to backup properly
- `Loupe-Loging` would ensure that a [third-party error management](http://www.gibraltarsoftware.com/loupe) agent is installed

You can mix and match server roles as needed.

![Server Roles in Otter](/resources/documentation/otter/roles.png){.screenshot}

## Role Dependencies {#role-dependencies data-title="Role Dependencies"}

Role dependencies allow you to model complex server and application configuration through hierarchical sets of simple roles with dependencies. This makes it much easier to share common configuration and define smaller-but-related roles.

You can define a role's dependencies (i.e., the other roles which are required) on the role overview page.

### Example: Accounts {#example-accounts}

To configure a server to have AccountsWeb, a web-based front-end for the Accounts application, that server must have IIS installed and configured. In Otter, you could represent this using two separate roles (accounts-web role and iis-server) and, using role dependencies, configure the accounts-web role to depend upon the iis-server role. This way, when you assign the accounts-web role to a server, the iis-server role will also be used to configure the server without needing to explicitly assign it to the server.

Expanding on the Accounts example further, we can define each application component as a separate role (accounts-db, accounts-svc, accounts-api, and accounts-web), and further separate out the non-accounts specific configuration into their own roles.

![Role Dependencies Example in Otter](/resources/documentation/otter/role-dependencies-ex.png){.screenshot}

We could create a "placeholder" role (accounts-full) that simply depends on the four component roles, and would allow the entire account stack to be installed on a single server. Or, different servers could be assigned different Account roles.

## Configuration Runtime Behavior {#config-runtime-behavior data-title="Runtime Behavior"}

When a configuration job is run against a server, Otter will assemble the server’s and its roles’ configuration plans into a single plan, and then execute it. Note that:

{.docs}
- Dependencies run first; when assembling the plan, each role’s dependencies (and each dependency’s dependencies) are run first to ensure required configuration happens before the rest.
- Circular dependencies result in runtime errors

Otherwise, the configuration jobs will run as if all roles were added to the server directly.

## Resource Pools {#resource-pools data-title="Resource Pools"}

A resource pool is a set of servers (defined by a role) that are used as a single, load-balanced resource. You may acquire an unused server from a resource pool, and then release it back into the pool once the needed tasks have been performed.

Visit the [Resource Pools documentation of the Inedo Execution Engine](/support/documentation/various/execution-engine/resource-pools) for more information on how to configure a resource pool.
