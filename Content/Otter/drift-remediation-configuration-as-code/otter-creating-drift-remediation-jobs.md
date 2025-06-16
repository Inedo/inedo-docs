---
title: "Creating Drift Remediation Jobs"
order: 2
---

Otter can [automatically remediate configuration drift](/docs/otter/drift-remediation-configuration-as-code/otter-automatically-remediate-configuration-drift) on your servers using Remediation Jobs. These jobs will use the same OtterScript that detected the drift to automatically reconfigure the server. 

This article will walk you through how to create drift remediation jobs for your servers in Otter.

## Configuring Drift Remediation on Servers
When server roles are assigned to a server, Otter can use the server role's Desired Configuration OtterScript to collect and compare the server's current (actual) configuration against the desired configuration (defined by the server roles). If the desired and current configuration aren't the same, then the server will be considered drifted, and you will be able to remediate that drift.

To configure a server to allow for Drift Remediation, first navigate to the server under “Servers”. Then, click on “Change” under Configuration drift.

![Configuration Drift Settings](/resources/docs/Configuring-Drift-Remediation-on-Servers-01.png){height="" width="50%"}
 
## Scheduling Remediation in the Future
When your server is configured to use “On-demand or scheduled remediation” and drift occurs, the following will be shown on the server page:

![Scheduling](/resources/docs/Scheduling-Remediation-in-the-Future-01.png){height="" width="50%"}

Clicking on “Remediate Drift” will display a page, allowing you to either immediately remediate drift or schedule it for later:

![Remediate Drift](/resources/docs/Scheduling-Remediation-in-the-Future-02a.png){height="" width="50%"}
 
## Remediating All or Partial Drift
Sometimes you may not want to remediate all the roles on a server or remediate all the servers in a role. You can perform “partial remediation” by targeting only specified servers or roles.

### Partial Remediation by Environment
1.	On the “Roles” page, click the "green play button" next to drifted role.
 
![Partial Remediation](/resources/docs/Partial-Remediation-by-Environment-01.png){height="" width="50%"}
 
2.	Use the “Environment filter” option to only remediate servers in a specified environment, instead of all drifted servers.
 
![Remediate Drift](/resources/docs/Partial-Remediation-by-Environment-02.png){height="" width="50%"}

### Partial Remediation by Role
Instead of remediating the configuration drift for all roles on a single server, you can specify to only remediate the drift for specified roles.

1.	First, navigate to the server with configuration drift. You can find this on the “Servers” page.

![Remediation by Role](/resources/docs/Partial-Remediation-by-Role-01.png){height="" width="50%"}
 
2.	On the Server Overview page, click on the green play button next to the drifted role you want to remediate.

![Remediation by Role](/resources/docs/Partial-Remediation-by-Role-02a.png){height="" width="50%"}

3. This will pop-up a window where you can remediate the drift now or in the future."

![Remediate Drift](/resources/docs/Scheduling-Remediation-in-the-Future-02a.png){height="" width="50%"}
 
4.	Once the Remediation Job runs, you’ll see that only the selected role will show up as “Current; any other roles that were drifted will remain in a drifted state.

![Sever Roles](/resources/docs/Partial-Remediation-by-Role-03.png){height="" width="50%"}

## Remediate on a Recurring Basis
You can also use a "Recurring Job" to remediate configuration drift on regular basis, such as nightly or weekly. This allows you to automatically ensure that your servers are in their desired configuration state, without having Otter immediately remediate drifted configuration the moment it is detected.

The main benefit to this is knowing that drift is remediated at specified, predictable intervals. This allows you to log-in to servers to make temporary configuration servers without running the risk of having that configuration being immediately remediated by Otter. After the configuration change, you can then edit the desired state in OtterScript.

### Creating Recurring Remediation Jobs
You can create a recurring job by navigating to Jobs > Upcoming & Recurring > Remediate Drift on a Recurring Basis.

![Recurring Job](/resources/docs/otter-remediation-recurringjob.png){height="" width="50%"}

See [Scheduled & Recurring Jobs](/docs/otter/orchestration-server-automation/otter-orchestration-scheduled-recurring-jobs) to learn more details about how recurring jobs work in Otter.

:::(Internal) (Delete This Image)
![](/resources/docs/Remediate-on%20a-Recurring-Basis.png){height="" width="50%"}
:::
