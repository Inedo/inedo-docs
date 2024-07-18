---
title: "What is an \"environment\" in Otter?"
order: 2
---

An environment is a logical grouping of [Servers](/docs/otter/connecting-to-your-servers-with-otter/otter-servers-in-otter), and generally describes one stage of the development process (development, testing, Q/A, production, staging, etc). But they can also describe other things like a physical location (NYC-DC1, Tokyo-DC1) or even a customer site. 

* **Server Targeting**; when running a [job](/docs/otter/orchestration-server-automation/otter-jobs-templates), you can specify environments as well as server roles so that you can easily target a range of servers at once, allowing you to scalably provision and configure servers
* **Restrict Access**; you can also [Permit or restrict access](/docs/otter/configuring-for-your-team/otter-administration-security) to environments to allow for self-service; for example, you could permit “QA Users” to run certain jobs against the Testing environment, while restricting them from  the Production environment.

Otter comes with three, built-in environments that represent a very simple deployment pipeline: Integration, Testing, and Production. You can create, rename, and delete environments as needed.

## Environments and Drift
Although you can't define a [Configuration plan](/docs/otter/scripting-in-otter/otter-otterscript-and-operations#configuration) for an environment, an environment can still be drifted if any of the servers within it are in a [Configuration Drift](/docs/otter/connecting-to-your-servers-with-otter/otter-servers-in-otter#drift).

## Nested Environments
Environments can have parent environments, which helps with visualization. Privileges and [Configuration Variables](/docs/otter/scripting-in-otter/otter-configuration-variables) will cascade from parent to child.

## Multiple Environments per Server
You can associate a server with more than one environment, though it's generally not recommended because it may create unexpected behaviors for other users in your organization. For example, let's say you configured `APISV1` to be in both the Staging and Production environments. Someone probably would expect that a job that targets production servers to target `APISV1` (which Otter would), but they may not want a staging job to target `APISV1` since it might already be used in production.

Also, both sets of permissions and restrictions will be applied, and if they overlap (one environment grants some things, while the other denies other things), then that will yield unpredictable and ambiguous behavior.

In addition, when a server is in multiple environments, then there can be no single environment in context when executing OtterScript against this server. This means that the variable function `$EnvironmentName` will return empty, and variables cannot be resolved. Instead, multiple roles are recommended.