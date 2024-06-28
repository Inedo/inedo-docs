---
title: "What is an \"Environment\" in BuildMaster?"
order: 2
---

An environment is a logical grouping of <a href="/docs/buildmaster-administration-agents-and-infrastructure-servers">Servers</a> and generally describes a stage of the development process (development, testing, Q/A, production, staging, etc.). However, it can also describe a physical location (NYC-DC1, Tokyo-DC1) or even a customer site.

Environments are also used in <a href="/docs/buildmaster/administration/users-and-security">security and access controls</a> to allow or restrict users from performing various tasks. For example, you can allow Q/A Users to deploy applications to the Testing environment, while restricting them from deploying to the Production environment.

BuildMaster comes with three built-in environments that provide a very simple deployment pipeline: Integration, Testing, and Production. You can create, rename, and delete environments as needed.    

## Nested Environments
Environments can have parent environments that you use to define a hierarchy of environments. For example, you can have Prod-Main and Prod-Backup both under the Production environment.

This not only helps with visualization, but also simplifies the access controls you define. For example, if you restrict access to the Production environment, Prod-Main and Prod-Backup would also be restricted unless you define more detailed access control for one of the child environments.

<a href="/docs/buildmaster/administration/configuration-variables">Configuration variables</a> will also cascade from a parent to a child environment, which means that deployments to a child environment will have access to the parent environment’s variables if they are not defined on the child environment.

## Multiple Environments per Server

You can associate a server with more than one environment, although this is generally not recommended because it can cause unexpected behavior among other users in your organization.

Also, both sets of permissions and restrictions are applied, and if they overlap (one environment grants some things while the other denies other things), then this leads to unpredictable and ambiguous behavior.