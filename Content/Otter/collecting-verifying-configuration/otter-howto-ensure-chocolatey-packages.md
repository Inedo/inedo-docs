---
title: "HOWTO: Ensure Chocolatey Packages with Otter"
order: 5
---

Manually provisioning, managing, and configuring hundreds of servers is time consuming and impractical. Otter automates this process by defining reusable sets of configurations called roles that can be assigned to any number of servers

Otter can "ensure" a Chocolatey package by making sure that it is installed or updated to a specified version. This guide will walk through how to use a role in Otter to automatically ensure Chocolatey packages are installed on your servers. 

## Prerequisites
In order to ensure Chocolatey packages, your servers will need to be added to Otter. 

If you haven't done so already, we recommend using the [Inedo Hub](/docs/installation/windows/desktophub-overview) to install Otter and the [Inedo Agent](/docs/inedo-agent/inedoagent-overview) to add your server to Otter. 

After the Inedo agent is installed, click the Create Server button on the Servers page, and enter the host name of the server, the port you installed the agent on, and the encryption key if you chose one. 

![Add Server to Otter](/resources/docs/Ensurechocolatey-addserver.png){height="" width="50%"}

Next your server will need to be configured to detect configuration drift so that Otter can automate your server management. 

This can be done by navigating to Servers> Servername> Overview> Details> Configuration Drift> Change and checking the box for Detect configuration drift as well as selecting your preferred Drift remediation method. 

![Edit Configuration Drift](/resources/docs/ensurechocolatey-configurationdrift.png){height="" width="50%"}

That's it! Your server is now connected to Otter. Simply repeat this process for any additional servers you wish to add to Otter.

## Step 1: Install Chocolatey Extension in Otter
Before Otter can ensure Chocolatey Packages, the Chocolatey extension will need to be installed.

Navigate to Administration Overview> Software System & Configuration> Extensions> Available Extensions> Chocolatey.

:::(Info) (Ensure Chocolatey is installed on all servers)
Make sure that you have Chocolatey installed on any servers connected to Otter, otherwise Otter will not be able to install and upgrade Chocolatey packages. Chocolatey can easily be installed via PowerShell script or other methods outlined by [Chocolatey.org](https://chocolatey.org/install).
:::

## Step 2: Create a Server Role 
A [server role](/docs/otter/connecting-to-your-servers-with-otter/otter-modeling-infrastructure-server-roles) contains a desired configuration that describes what you want the server to do, what needs to be installed, or any other configuration. In this case we want Otter to “ensure” that several Chocolatey packages are installed on our servers. For the purposes of this guide, we'll be ensuring the following Chocolatey packages:

* [7-Zip 21.7 ](https://community.chocolatey.org/packages/7zip)
*  [Notepad++ 8.3.3](https://community.chocolatey.org/packages/notepadplusplus.install)
*  [Bowpad 2.8.2](https://community.chocolatey.org/packages/bowpad)
*  [GoToMeeting 10.19.19950](https://community.chocolatey.org/packages/gotomeeting)

In other words, we want our server role to tell Otter to check that the above Chocolatey packages are installed on a server and install or update them if they are not. If we want to install other packages, or upgrade package versions, we simply need to edit this desired configuration, and Otter can install those packages.

To create a server role, navigate to Roles> Create Server Role. Enter an appropriate name like "common-windows-packages" and a description if desired. 

![Create Server Role](/resources/docs/ensurechocolatey-createrole.png){height="" width="50%"}

Next you will need to create a configuration plan for the role by navigating to Roles>Role Name> Overview> Edit Desired Configuration. This will take you to the Otter Script Editor pictured below.

![Otter Script Editor](/resources/docs/Otterscript-editor.png){height="" width="50%"}

Use the search bar in the top left to find the statement titled "Ensure Chocolatey Package", then drag-and-drop into the container to the right. You will then be prompted to enter the details of the Chocolatey package you want to ensure.

![Ensure Chocolatey Package](/resources/docs/otter-chocolatey-ensurepackage.png){height="" width="50%"}

Note that you can also "Switch to Text Mode" in the bottom right to utilize scripts. This can be very useful for quickly setting up roles rather than drag-and-dropping the info for each Chocolatey package. 

![Text Mode](/resources/docs/Otterscript-texteditor.png){height="" width="50%"}

:::(Info) (Chocolatey Package Source Options)
If you look at the screenshot from the visual mode of the Otter Script Editor you'll notice that the source is the public Chocolatey repository. You can also pull packages from a private Chocolatey repository (such as a [Chocolatey feed in ProGet](/docs/proget/feeds/chocolatey/howto-chocolatey-internalized)) like we did in the screenshot for the text mode. 
:::

## Step 3: Assign the Server Role to Appropriate Servers
Now that your server role is configured it needs to be assigned to the appropriate servers.

Navigate to Servers> Servername> Assigned Server Roles> Assign roles and select the role you created. 

![Assign Server Roles](/resources/docs/Ensurechocolatey-assignserverroles.png){height="" width="50%"}


## Step 4: Check Configuration Drift
"[Configuration Drift](/docs/otter/collecting-verifying-configuration/otter-desired-configuration-with-otterscript)" is what happens when a server's current (actual) configuration is different than its desired configuration. Checking configuration drift will tell Otter to detect if the roles you have assigned to the server are "current" or "drifted".

Since we have assigned a role to our server to ensure the Chocolatey packages in step 2, we can expect one of two outcomes. If one or more of the requested Chocolatey packages are not installed on the server then the server status will be labeled as "Drifted". However, if all the Chocolatey packages are installed on the server, then the server status will be labeled as "current", implying that the server is currently reflecting what is requested by the server roles.

To check your configuration, navigate to Servers> Servername> Check Configuration. 

![Check configuration](/resources/docs/ensurechocolatey-checkconfig.png){height="" width="50%"}

As the Chocolatey packages have not yet been installed, Otter will detect that your server configurations have drifted. 

![Drifted Servers](/resources/docs/Ensurechocolatey-serversdrifted.png){height="" width="50%"}

 ## Step 5: Remediate Configuration
 Once a configuration drift is detected, remediation can be utilized to automatically fix your server according to the server roles you have assigned it. Since we set a server role to ensure Chocolatey packages in step 2, remediating configuration would mean installing the Chocolatey packages specified in the server role. 
 
Navigate to Servers> Server Name> Remediate drift to automatically reconfigure your server to follow the assigned roles.

![Remediate Drift](/resources/docs/ensurechocolatey-remediatedrift.png){height="" width="50%"}

That's it! When remediating the configuration drift of your servers, Otter recognized that they have the role “common-windows-package” assigned to them and automatically installed the Chocolatey packages at the push of a button. 

![Remediated Servers](/resources/docs/ensurechocolatey-servers.png){height="" width="50%"}

Remember that configuration drift can be setup to be remediated "immediately upon detecting drift" preventing you from having to even push a button!

## Troubleshooting

### I got an error message in the server status
![Error message](/resources/docs/ensurechocolatey-errormessage.png){height="" width="50%"}

If your server status says "error", click on the underlined "had an error" to view the execution log. If the error says "There was an error executing chocolatey" it's likely that Chocolatey is not installed on the server.  

Chocolatey can easily be installed via PowerShell script or other methods outlined by [Chocolatey.org](https://chocolatey.org/install). Simply install Chocolatey on your server then check your configuration again. 

### I don’t see any Chocolatey statements in the OtterScript Editor

If you’re in the OtterScript Editor configuring your server role (step 2) and you can't seem to find Chocolatey anywhere, this is likely because the Chocolatey extension was not installed in Otter. 

Follow the instructions in step 1 to install the Chocolatey extension then return to the Otter Plan Editor and the Chocolatey statements will appear as an option.  
  