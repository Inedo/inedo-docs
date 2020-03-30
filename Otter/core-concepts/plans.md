---
title: Plans
subtitle: Plans in Otter
keywords: otter,plans,operations
sequence: 200
show-headings-in-nav: true
---

Plans are the instructions that tell Otter exactly how to provision and configure your servers.

There are three types of plans:

{.docs}
- **[Configuration Plans](#configuration)** - *describe* the configuration of [Servers] and [Server Roles], and are continuously run to monitor and (optionally) remediate [Configuration Drift]
- **[Orchestration Plans](#orchestration)** - run on a scheduled or recurring basis, these have a much broader use and can deploy changes to servers, interact with cloud services, provision virtual machines, etc.
- **[Modules](#modules)** - are parameterized sets of instructions that can be used by configuration plans, orchestration plans, or other modules

All types of plans are represented as [OtterScript] text files, and run by the [Inedo Execution Engine].

## Using the Visual Plan Editor

With the drag-and-drop editor, you really don't need to learn OtterScript before you start building a plan. You can switch back-and-forth between visual and text modes to get a feel for the syntax and structure of the language pretty quickly.

For example, here's a basic plan with several statements that declare the desired state of configuration for an "Accounts" application.

<tab-block>
    <tab name="Visual Mode">
        <img class="screenshot" src="/resources/documentation/otter/plan-blocks.png" alt="An IIS Plan Block (Visual Mode)" />
    </tab>
    <tab name="Text Mode (OtterScript)">
        <img class="screenshot" src="/resources/documentation/otter/plan-block-text.png" alt="" />
    </tab>
</tab-block>

This plan could be created as a configuration plan (either on a server or role), or as an orchestration plan.

## Plan Types

The main difference between these types of plans is the context in which they are used.

### Configuration Plans {#configuration data-title="Configuration Plans"}

Configuration plans collect the *actual* configuration of a server as it relates to your plan. For example, if the above block were placed in a configuration plan for a server (or on one of the server's server roles), then that server's configuration would look like this:

![An IIS Plan Collected Configuration](/resources/documentation/otter/plan-iis-config.png){.screenshot}

Configuration plans are run routinely, and Otter would run the above plan in the following manner:


1. Read the `HOSTS` file and collect the value of the `accounts.local` entry
2. Resolve the `$WebsitesRoot` variable, which could be a [Runtime][Runtime Variables] or [Configuration variable][Configuration Variables] whether set on the role, server, [Environment][Environments], etc
3. Read the `web.config` file, and collect the value of the `ConnectionString` entry
4. Read the `Runtime` and `Pipeline` values of the `AccountsAppPool`
5. Read the `AppPool`, `Path`, `Protocol`, and `Biding` values of the `Accounts` site
6. Store the collected configuration in the database
7. Compare the collected configuration against the declared configuration
8. If there is configuration drift, and the server is configured to *automatically remediate drift*:
   {.docs}
   - Create or update the actual configuration based on the plan
   - Store the new configuration in the database

### Real-world Configuration

Of course, the idea of automatic change is not exactly palatable to many organizations, which is why servers do not automatically remediate configuration drift unless you have explicitly configured them to do so. By default, you will instead remediate drift through a scheduled or on-demand [Job](jobs). You can even [Simulate](jobs#simulation) the execution, just to see exactly what would happen.

Also, the real world rarely works declaratively, and configuration plans often need a mix of declarative statements (like ensure a file is in a specific location) and imperative or procedural operations (like starting or stopping a service).

### Orchestration Plans {#orchestration data-title="Orchestration Plans"}

Orchestration plans are quite a bit simpler: they just use the same, advanced execution engine to execute operations and scripts on servers.

Unlike configuration plans, orchestration plans don't run continuously. Instead, you scheduled them to run (either in the future, or on a recurring basis) using an [Orchestration Job](jobs#orchestration).

This simplicity also makes them hard to describe in detail, because they can be used to do just about anything:

{.docs}
- Installing patches or other changes to 1000's of servers
- Spinning up and configuring multiple virtual servers
- Execute existing scripts in a controlled and visible manner
- Provisioning bare-metal hardware
- Deploying [ProGet](/docs/proget) packages

### Modules {#modules data-title="Modules"}

Modules are parameterized sets of instructions that can be used by configuration plans, orchestration plans, or other modules.

[Servers]: /docs/otter/core-concepts/servers
[Server Roles]: /docs/otter/modeling-infrastructure/server-roles
[Configuration Drift]: /docs/otter/core-concepts/servers#drift
[OtterScript]: /docs/otter/reference/otter-script
[Inedo Execution Engine]: /docs/otter/execution-engine/overview
[Runtime Variables]: /docs/otter/execution-engine/runtime-variables
[Configuration Variables]: /docs/otter/global-components/configuration-variables
[Environments]: /docs/otter/modeling-infrastructure/environments
