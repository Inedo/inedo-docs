---
title: "💩 Automatic configuration scanning and verification"
order: 2
hidden: true
---

:::(Internal) (DISASTER)
This article is a mess, and should be scrapped and rewritten, following Inedo's documentation standards and traning.

It was intended to be "Tutorial Content", with an outline as follows:

[intro]
- <merge/edit to keep consistency with the Desired Configuration documentation>
- Otter can automatically and continuously scan your servers to verify that their actual (current) configuration matches the desired state of configuration.
- For example, if you want insecure ports like HTTP to be blocked on some servers, you can define that as [Desired Configuration with OtterScript], and Otter will check those servers.
- <screenshot of Collected Configuration showing drift on HTTPS (from OOBE demo)>

- This is step-by-step to <summarize/link steps>

## Create A Server Role
- describe what server role
- instructions on creating `secure-ports` role
  - "Ensures that unsecure, inbound ports like HTTP are blocked on Windows Firewall, and secure ports are open."
- note this will be assigned to a server later 
- note you can add variables and dependencies later

## "Desired Configuration" in OtterScript
- describe "desired configuration"
- describe OtterScript
- instructions on how to edit role's config

### Verifying Firewall Rules
- describe what "Ensure Firewall Rule" does
- its one of many operations; give other examples 
- instructions on how to add it visually
- <screenshot or rule>
- mention visuall-to-text
- <otterscript of rule>

### Verifying Configuration with PowerShell
- Otter can also use your existing PowerShell script to verify server configuration
- this is done with `PSVerify` -- see [Compliance as Code with PowerShell & PSVerify] to learn more

## Connect Servers to Otter
- summarize servers and agents docs
- link to docs

## Configure Servers for Verification
- describe "Configuration drift" setting
- instructions on changing to Verify / report only and then assign role

## Detecting & Investigating Configuration Drift
- describe what drift is
- show screenshots of drifted configuration of demo role
  - show logs where drift occurred
  - describe what you're seeing in shots

### Manually Checking Configuration
- mention that configuration checks occur every hour
  - configurable in advanced setting
- you can manually check a server or a role with button

## Expanding to Multiple Roles & Dependencies
- describe multiple roles and dependencies
- give/summarize advice from bootcamp on multiple roles
:::

This article will walk you through how to define a [desired configuration](/docs/otter/collecting-verifying-configuration/otter-desired-configuration-with-otterscript), so that Otter can routinely scan your servers to see if their current (actual) configuration matches their desired state.

For example, if you wanted insecure ports like HTTP to be blocked on some servers, Otter can show you which servers it's not blocked on, and report that as "drift".

![](/resources/docs/Server-Roles-and-Configuration-Drift.png)
	
## Create A Server Role
Otter uses server roles to collect information about your servers and tells you if their configuration is drifted (i.e. matches the desired configuration). You can assign any number of roles to any number of servers.

### How to Create a Server Role
You can create a server role by going to “Roles” and clicking “Create Server Role.” The  “Desired Configuration” tab will contain OtterScript that describes the configuration that servers assigned to that role should have. 

For example, if you wanted servers to have a firewall rule that blocked the insecure inbound HTTP port 80 while keeping the secure port HTTPS port 443 open, you could create a server role called `secure-ports`.

![](/resources/docs/secure-ports%20config.png){height="" width=""}

You can add [configuration variables](/docs/otter/scripting-in-otter/otter-configuration-variables) and [role dependencies] later. 

![](/resources/docs/secure-ports-overview.png){height="" width=""}

### Assigning to Servers
After creating a server role, you'll need to assign it to the servers that you'd like to scan and verify your desired configuration. To do this, click on “Assign Server” and select servers you want to apply this rule to. 

Note that, although the server role has been created, it won't scan any servers until you [assign it to a server]. 

## "Desired Configuration" in OtterScript
OtterScript is Inedo’s Domain Specific Language and can be used to easily specify a desired configuration using a drag-and-drop visual editor or text-based input. 

![](/resources/docs/secure-ports-visual-text-mode.png){height="" width=""}

OtterScript is used to create and modify server role configurations.

### Using Visual Mode 
From the left-hand pane, select the functions you want to include in your configuration plan and drag them to the main edit area. You’ll be asked to declare the required values for each function you want to include.

Otter's Visual Mode is very easy to use and accessible to anyone with any level of programing experience.

![](/resources/docs/secure-ports-visual-mode%281%29.png){height="" width=""}

### Using Text Mode
Alternatively, you can use Text Mode. While using Text Mode, you can directly type OtterScript commands and/or PowerShell to create a set of instructions for your desired configurations.

![](/resources/docs/secure-ports-text-mode.png){height="" width=""}

### Verifying Firewall Rules
After setting your Desired Configuration, OtterScript will use the `Ensure Firewall Rule` operation to ensures the existence of a firewall rule on a Windows server.

This is one of many operations that you can execute using OtterScript. Other operations include `Remediate Drift` which checks for configuration drift. If a drift is detected, the operation automatically triggers a [remediation job] (Automatic drift remediation).


### Verifying Configuration with PowerShell
Otter can also use your existing PowerShell script to verify server configuration. This is done with `PSVerify` -- see [Compliance as Code with PowerShell & PSVerify] to learn more.

## Connect Servers to Otter
You can set up Otter to communicate with servers that have the Inedo Agent installed or you can use "agent-less" communication with PowerShell remoting (WMI) or with SSH.

After you've configured a server to be used with Otter, you can add new servers to Otter by navigating to the “Servers” page and clicking “Add Server”. On the subsequent pop-up, you’ll need to select from your preferred connection method and enter all required information.

![](/resources/docs/add-server.png){height="" width=""}

## Configure Servers for Verification
After clicking to the “Overview” page of a server in Otter, you can change the configuration drift settings under Details. This governs if Otter will detect drift on a server and how it will react.

You can choose one of the following options for drift remediation:
•	Do not remediate (report only)
•	On-demand or scheduled remediation
•	Immediately upon detecting drift

You need to assign one or more Server Roles by clicking the “assign roles” button for Otter to know if a server is drifted, 

![](/resources/docs/server-overview.png){height="" width=""}

 ## Detecting & Investigating Configuration Drift
In Otter, drift occurs when a server’s settings deviate from its desired configuration. This either through human input or automatically when installing certain applications.

Note that desired configuration in Otter needs to be updated per the accrual requirements of the server. For example, if a new application requires a previously closed port to be open this change needs to be reflected in Otter or the port will automatically be closed again during the next drift remediation process.

![](/resources/docs/drifted-cofig.png){height="" width=""}

![](/resources/docs/drift-detected-log.png){height="" width=""}

*Short explanation of the screenshots*

### Manually Checking Configurations
Otter will check configurations every hour by default. However, you can chang this under `Administration > Advanced Settings`.

![](/resources/docs/Advanced%20settings.png){height="" width=""}

You can also manually check a server’s ore role’ configuration by navigating to the desired server or role and clicking “Check Configuration”.

## Expanding to Multiple Roles & Dependencies
You can assign Multiple Roles to a single server to accurately reflect its real-world purpose. For Example, you could have a server that is assigned a `secure-ports` and `ftp-server` role.

**Role Dependencies** enable users to define granular roles that depend on - or are dependent of other roles. For example, the `ftp-server` role could depend on `secure-ports` to ensure that only FTP ports are open on the server. If `secure-ports` drifts, `ftp-server` will also report as drifted, even if the role’s desired configuration matches the server’s actual configuration.


:::(Internal) (DELETE THESE SCREENSHOTS)
So
![](/resources/docs/Drifted-FTP.png){height="" width=""}

![](/resources/docs/secure-ports-visual-mode%281%29.png){height="" width=""}
![](/resources/docs/secure-ports-text-mode.png){height="" width=""}
![](/resources/docs/Drifted-FTP.png)
![](/resources/docs/secure-ports-overview.png){height="" width=""}
![](/resources/docs/secure-ports-visual-text-mode.png){height="" width=""}
:::
