---
title: "Automatically Remediate Configuration Drift"
order: 1
---

Otter can detect when your servers have drifted from their [desired configuration state](/docs/otter/collecting-verifying-configuration/otter-desired-configuration-with-otterscript) using OtterScript. 

You can use the same OtterScript scripts to reconfigure your servers into your desired state on your time, on-demand, or immediately upon detection.

## What is "Automatic Drift Remediation" in Otter? 
Drift remediation means reconfiguring a server to bring it to its desired state. Automatic remediation in Otter allows you to reconfigure servers by:
  - changing your desired configuration (for example: adding a firewall rule to your OtterScript)
  - having Otter detect drift (the server won't have that firewall defined)
  - using Otter to fix that drift (Otter will create the firewall rule on the server).

This automatic process is faster and easier than doing these steps manually.

## Configuring Servers Automatic Remediation

To configure a server for automatic remediation, navigate to Servers > Configuration Drift. You can then choose from three options:
   - **Report-only**; this option is best if you want complete, manual control over drift remediation. Otter will just report on drift without remediating it.
   - **On-demand**; this option is best for scheduling Otter’s remediation for certain days and/or times. Otter will, for example, wait until the middle of a weekend night (during normal downtime)
   - **Immediately**; this option will immediately reset drifted servers upon drift detection and should be used with caution (see next section)

Configuring one of these options allows you to remediate drift on a single server or server role.

:::(Warning) (Caution with Remediating Drift “Immediately”)
Note that automatic remediation (“Immediately”) can lead to unexpected and unwelcome configuration changes, especially when other teams are also making configuration changes. To avoid inter-team confusion, we recommend that you wait until all teams are familiar with desired state management before using immediate automatic remediation.
:::

## Remediating Configuration on a Single Server
When remediating a single server, if Otter has detected drift, you'll see the "Remediate Drift" button.

Clicking this causes Otter to run a remediation job that targets that one server, which can be run immediately or at a certain start time.

![Single Server Configuration](/resources/docs/otter-remediation-singleserver.png){height="" width="50%"}

When the job runs, Otter will assemble the configuration plans of both the servers and their roles into a single plan and then execute it. 
•	Dependencies run first to ensure required configuration happens before the rest. Circular dependencies result in runtime errors
•	Otherwise, the configuration jobs will run as if all roles were added to the server directly.

Note that Otter will only run the sections where drift is detected.
 
## Remediating Configuration on a Server Role
When remediating drift on a server role, you'll see "Remediate Drift" button on that role. This will create a job but with a different behavior that a server: Instead of building a configuration plan based on all servers, it will run only that role and its dependencies.

Otter will target all servers in role, but you can filter by environment. For example, all servers in Test will be remediated immediately. If all remediation in Test is successful, all servers in Production will be remediated at 02:00A.

You can target single server/server role on by clicking the play button 

## Remediating Servers via API
The [Server Configuration Management API](/docs/otter/reference/api/configuration-management) offers a simple mechanism for querying the configuration status of servers, environments, and server roles, as well as remediating drift.

For security and simplicity, these endpoints require that you first create an API Key.

## Simulating Remediation
If you want to dry-run a remediation, run it in [simulation mode](/docs/otter/orchestration-server-automation/otter-jobs-templates). 

![Simulating Remediation](/resources/docs/otter-remediation-simulation.png){height="" width="50%"}

Simulation jobs will not perform any action on the target server. They just demonstrate how the remediation job would be carried out as configured based on the entered variables.

For example, the simulation job would be marked a success as long as the entered variables match a valid server target. It would not verify whether the remediation would be successful, as that would require performing an action on the target server.

:::(Internal) (Potential Improvement)
Add " Targeted Remediation and Permissions " section that talks about environment restrictions and task attribute requirements.
:::


## Remediate Drift on a Recurring Basis
Remediating drift on a recurring basis strikes a balance between “immediate” configuration and “on-demand.” 

Select "recurring drift remediation" by navigating to Jobs and select Remediate Drift on a Recurring Basis. 

![Recurring Basis](/resources/docs/otter-remediation-recurringbasis.png){height="" width="50%"}

You must select the schedule and server targeting for this job.

## OtterScript Behavior (Ensure Operations and Blocks)
Just about everything in OtterScript is accomplished with operations. 

Operations represent a discreet configuration item or task that is typically run against the server in context (you can also write your own operations using the Inedo SDK). Broadly, there are two types of operations:

•	**Ensure** – defines a desired state of configuration, gathers configuration information from a server, and configures or changes a server to match the desired state if the actual and desired configurations differ
•	**Execute** – changes something on a server

Ensure operations enable Otter to use Declarative Configuration Management.

In addition to operations, OtterScript has statements and blocks.

•	**Statements** can be "Ensure Operations" like the `Firewall::Ensure-NetFirewallRule` Operation or can "Execute Operations" like "Sleep"
•	**Blocks** can contain statements and other blocks.
When an Operation reports drift, then the block is marked as "drifted," and the entire block is considered drifted.

:::(Warning)
You must put Ensure Operations in a configuration plan. If you're not collecting any configuration, then you can’t execute the configuration that changed.
:::

To learn more, see [OtterScript & Operations](/docs/otter/scripting-in-otter/otter-otterscript-and-operations).
