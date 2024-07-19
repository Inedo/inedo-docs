---
title: "What is a \"Server Role\" in BuildMaster?"
order: 3
---

A role describes a purpose or function that a server can have, and is used as a deployment target so that you do not have to explicitly specify server names.

For example, if you deploy to the role `hdars-api-host` in the test environment, the deployment will be to all servers with that role and in that environment. This can be especially helpful if your application is deployed to multiple servers, such as in a load-balanced environment.

## Creating and Assigning Server Roles
You can create, edit, and delete server roles using the web-based interface (Admin > Infrastructure > Roles), or programmatically with the [Infrastructure API.](/docs/buildmaster/reference/api/infrastructure)

You can assign a role to a server by editing the list of servers assigned to that role (Role > Edit Details), or by editing the list of roles assigned to a server (Server > Edit Roles).

## Deploying to Server Roles

There are two ways to deploy to a server role:

### Pipeline Stage Target

You can specify a role name instead of a list of servers as a stage target in a [pipeline stage](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines#Pipeline-stages). When the build is deployed to that stage, the stage target's deployment plan will be run against all servers with that role and in that environment.

### Server Roles and OtterScript

You can deploy to servers in a role using the `@ServersInRole` function within a [loop block](/docs/executionengine/otterscript/statements-and-blocks/loop).

        foreach server in @ServersInRole(hdars-api-host)
        {
            Deploy-Artifact hdars-api-service
            (
                To: C:\\HDars\\App\\api-service
            );
        }
   

## Role Dependencies
Role dependencies are used by [Otter](https://inedo.com/) to model complex server and application configurations through hierarchical sets of simple roles with dependencies. This makes it much easier to share common configuration and define smaller (but related) roles.

While you can define a role's dependencies (i.e., the other roles that are required) on the role overview page, BuildMaster won't behave any differently if a role has dependencies. They are just included so you can [synchronize your infrastructure](/docs/buildmaster/administration-agents-and-infrastructure/sync) across products.

## Example: Multiple Server Roles

Let's say you have a complex, multi-tier system called **HDars** that's designed to scale and run across multiple servers. You may have the following roles defined:

*   HDars-cloud hosts the middle-tier, cloud-data processing components
*   HDars-web hosts the web front-end for HDars that end-users will access
*   HDars-database is where the database components are hosted

These server roles may be applied to different servers in different environments.


| Server | Environment | Roles |
| --- | --- | --- |
| `INT1` | Integration | `HDars-cloud, HDars-web, HDars-database` |
| `TESAPP!` | Testing | `HDars-cloud, HDars-web` |
| `TESTDB1` | Testing | `HDars-database` |
| `PRODHDARSWEB1` | Production | `HDars-web` |
| `PRODHDARSWEB2` | Production | `HDars-web` |
| `PRODHDARSAPI1` | Production | `HDars-cloud` |
| `PRODDBSV1` | Production | `HDars-database` |
