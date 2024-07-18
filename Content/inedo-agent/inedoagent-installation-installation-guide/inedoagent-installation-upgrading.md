---
title: "Upgrading"
order: 5
---

Because the Inedo Agent is essentially a lightweight host for product-specific agents (see [General Architecture](/docs/inedo-agent/inedoagent-overview#arch)), updating the Inedo Agent is usually not necessary nor advised.

:::(Internal) (Currently Broken)
To help upgrade the Inedo Agent, BuildMaster and Otter include a feature that [automatically updates the Agents](/docs/installation/agents-and-servers#automatic-updates). You can also upgrade an Inedo Agent by running the installer (or by following the manual process) on the server itself.
:::

## When to Upgrade 
:::(Warning)
You shouldn't upgrade unless you are directed by a version of your Inedo tool (Otter, BuildMaster, etc) or an Inedo support engineer.
:::
When we introduce that feature in our products, we will update this documentation to explain why.

If you came across this page through browsing or searching, and you are getting random agent errors, you are certainly welcome to try a different version.

However, it might not help, and might not be worth your time to upgrade. While it probably won't introduce regressions, you may prevent an error from happening in the future.

## Upgrade Risk/Notes 

All changes between [versions](/docs/inedo-agent/inedoagent-versions) have been small bugfixes, and would have been considered *maintenance* releases. Down the line, we may change versioning schemes to be more reflective of the scope of change, especially if we make a *minor* or *major* level change.