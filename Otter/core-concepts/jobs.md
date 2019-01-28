---
title: Jobs
subtitle: Jobs & Scheduling Changes
keywords: otter,jobs,executions
sequence: 300
show-headings-in-nav: true
---

Jobs allow you to [remediate configuration drift][Plans] and [run orchestration plans][Plans], either on-demand, at a scheduled time, or on a recurring basis. They can help to minimize downtime and can be set to run when convenient or most appropriate for your enterprise. Jobs give you the control of when, where, and how your infrastructure changes.

Jobs ultimately target [Servers], and can do so either directly (by specifying a list of servers), or indirectly (by specifying [Server Roles] and/or [Environments]). Just as there are two types of plans, there are effectively the same two types of jobs:

{.docs}
- **[Configuration Job](#configuration)** - remediates configuration drift by bringing the targeted servers to their desired configuration
- **[Orchestration Job](#orchestration)** - runs a specified orchestration plan against the targeted servers

Either type of job can be run immediately (for example, test servers in unexpected drift), at scheduled times in the future (planned changes to a production server), or on a recurring basis (adding more servers at known peak times).

## Configuration Jobs {#configuration data-title="Configuration Jobs"}

When there is [configuration drift] on a server or role (and you have permission to create a configuration job), you can simply click the *Remediate Drift* button to schedule a configuration job.

![Remediation Job in Otter](/resources/documentation/otter/jobs-remediate.png){.screenshot}

You can also create a configuration job by clicking on *Jobs* > *Create Job*, or create a recurring configuration job (such as updating production servers once a week) by <i>Jobs</i> > *Recurring* > *Create Recurring Job*.

## Orchestration Jobs {#orchestration data-title="Orchestration Jobs"}

Orchestration jobs are created on the jobs page, and require the selection of an orchestration plan and servers to run against.

![Orchestration Job in Otter](/resources/documentation/otter/jobs-orchestration.png){.screenshot}

#### Orchestration Job Context {#orchestration-job-context data-title="Orchestration Job Context"}

Orchestration jobs may be created to target one of the following:

- Servers directly
- Roles and/or environments
- No servers/roles/environments *(i.e. the plan is unmodified and executed as-is)*

When targeting servers directly, each server is enumerated and the plan is executed for each server in parallel. Targeting roles and/or environments has the following behavior:

- **Server role(s) only** - behavior is equivalent to targeting servers directly for all servers associated with any role in the list
- **Environment(s) only** - behavior is equivalent to targeting servers directly for all servers associated with any environment in the list
- **Server role(s) and environment(s)** - will enumerate all servers in a role that are also in any listed environments, if multiple roles are supplied and a server belongs to more than 1, execution may occur on that server multiple times, use the for role context statement to ensure certain parts of a plan only execute when a specific role is in context

### Job Templates {#job-templates data-title="Job Templates"}

You can also define a job template under *Admin* > *Job Templates*; these can be used to pre-populate fields in an orchestration job, and also allow for jobs to be triggered using the [Job Orchestration API].

![Job Templates in Otter](/resources/documentation/otter/job-template.png){.screenshot}

## Simulation Job Runs {#simulation data-title="Simulations"}

You can run a job in a simulation mode, which will run all the steps in a plan without making any changes to the targeted servers. When a simulation is run, Otter will still follow the logic you defined in your plan, and log all the things it's doing, and the changes that *would* have been made if the job was running normally. This can be helpful when running jobs against unknown servers, remediating unexpected drift, or performing changes on high value servers.

![Simulation Job in Otter](/resources/documentation/otter/jobs-simulate.png){.screenshot}

Simulating a job can help ensure future accuracy by finding any errors that might have occurred in a plan, without affecting current configurations.

[Plans]: /support/documentation/otter/core-concepts/plans
[Servers]: /support/documentation/otter/core-concepts/servers
[configuration drift]: /support/documentation/otter/core-concepts/servers#drift
[Server Roles]: /support/documentation/otter/modeling-infrastructure/server-roles
[Environments]: /support/documentation/otter/modeling-infrastructure/environments
[Job Orchestration API]: /support/documentation/otter/reference/api/jobs
