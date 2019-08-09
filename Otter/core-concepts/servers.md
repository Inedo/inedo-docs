---
title: Servers
subtitle: Servers in Otter
keywords: otter,servers
sequence: 100
show-headings-in-nav: true
---

Otter helps you provision and configure your servers automatically, without ever needing to log-in to a command prompt.

:::attention {.best-practice}
A server can be physical (bare metal), virtual, or even nonexistent (i.e. one that you will provision later).
:::

## Adding Servers to Otter {#adding-servers data-title="Adding Servers"}

You can add a server using the web-based user interface (Servers > Add Server), or programmatically with the [infrastructure API](/docs/otter/reference/api/infrastructure).

![Create Servers](/resources/documentation/otter/2/servers.png){.screenshot}


Otter communicates with servers using the [Inedo Agent](/docs/various/inedo-agent/the-agent) (for Windows) or [SSH/SFTP](https://www.ssh.com/ssh/sftp/) (for Windows and Linux).

## Provisioning and Configuring Servers {#provision-server data-title="Provisioning and Configuring Servers"}

Once a server has been added, you can begin provisioning and configuring the server by describing the packages, settings, files, and anything else that your server requires. This is accomplished by creating a [configuration plan](/docs/otter/core-concepts/plans#configuration) that describes the desired state of configuration and the specific steps needed to ensure that state.

For example, you would use [Ensure-AppPool](/docs/otter/reference/operations/iis/ensure-app-pool) to describe and provision an Application Pool in IIS, along with any number of the three dozen settings application pools can have.

<tab-block>
    <tab name="Visual Mode">
        <img class="screenshot" src="/resources/documentation/otter/2/ensure-app-visual.png" alt="Visual Mode" />
    </tab>
    <tab name="Text Mode">
        <img class="screenshot" src="/resources/documentation/otter/2/ensure-app-text.png" alt="Text Mode" />
    </tab>
</tab-block>

You can create a configuration plan for an individual server, or use a role to share configuration plans across any number of servers.

## Configuration Drift {#drift data-title="Configuration Drift"}

Otter continuously monitors you servers for configuration changes, and reports when there's configuration drift. You can set servers to automatically remediate drift, or schedule remediation and other configuration changes as needed.

:::attention {.analogy}
![](/resources/images/icons/analogy.png)

Configuration drift is when a server's *actual* configuration is different than the *desired* configuration. It can occur expectedly (if you change the configuration plan), or unexpectedly (someone manually made a change on the server).
:::

![Servers in Drift](/resources/documentation/otter/2/drift.png){.screenshot}

Each server can be configured to manage configuration drift differently, through one of these options:

{.docs}
- Do not collect: the server's *actual* configuration is not collected
- Report Only: when configuration drift is detected, the server will be changed into a drifted state, and you can schedule a [configuration job](/docs/otter/core-concepts/jobs#configuration) (either real or simulated) to remediate the drift
- Automatically Remediate: when drift is detected, the server will automatically be configured with the desired configuration state

### Server Packages {#packages data-title="Server Packages"}

Otter will also collect information about the packages (such as Universal, Chocolatey, PowerShell, etc.) that are installed on any given server. This means you don't have to log in to each server to view the current configuration and installed packages.

To configure a server package collection, go to the *Packages tab* on the server's overview pages, and click the *configure* button. You will be prompted for which types of packages to collect:

![Package collection](/resources/documentation/otter/2/package-collection.png){.screenshot}

When configured, Otter will regularly collect information about installed packages and display it in an easy-to-read report.

![Universal packages installed](/resources/documentation/otter/2/universal-package-installed.png){.screenshot}
