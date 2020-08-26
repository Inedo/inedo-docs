---
title: Environments
keywords: otter,environments
show-headings-in-nav: true
---

An environment is a logical grouping of [Servers](../core-concepts/servers), and generally describes one stage of the development process (development, testing, Q/A, production, staging, etc). But they can also describe other things like a physical location (NYC-DC1, Tokyo-DC1) or even a customer site. You can also [Permit or restrict access](/docs/otter/administration/security) to servers and [Configurations](/docs/otter/core-concepts/plans) within particular environments to ensure enterprise security and compliance needs.

![Environments in Otter](/resources/documentation/otter/environments.png){.screenshot}

Otter comes with three, built-in environments that represent a very simple deployment pipeline: Integration, Testing, and Production. You can create, rename, and delete environments as needed.

## Environments and Drift {#environments-drift data-title="Environments and Drift"}

Although you can't define a [Configuration plan](../core-concepts/server#configuration) for an environment, an environment can still be *drifted* if any of the servers within it are in a [Drifted state](/docs/otter/core-concepts/servers#drift).

## Nested Environments {#nested-environments data-title="Nested Environments"}

Environments can have parent environments, which helps with visualization. Privileges and [Configuration Variables](../global-components/configuration-variables) will cascade from parent to child.

## Multiple Environments per Server {#multiple-environments data-title="Multiple Environments"}

You can associate a server with more than one environment, though it's generally not recommended because it may create unexpected behaviors for other users in your organization. For example, let's say you configured `APISV` to be in both the `Staging` and `Production` environments. Someone probably would expect that a job that targets production servers to target `APISV` (which Otter would), but they may not want a staging job to target `APISV` since it might already be used in production.

Also, both sets of permissions and restrictions will be applied, and if they overlap (one environment grants some things, while the other denies other things), then that will yield unpredictable and ambiguous behavior.

In addition, when a server is in mutliple environments, then there can be no single environment in context when executing OtterScript against this server. This means that the variable function `$EnvironmentName` will return empty, and variables cannot be resolved. Instead, multiple roles are recommended.
