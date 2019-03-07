---
title: Getting Started with BuildMaster
keywords: buildmaster
sequence: 10
---

BuildMaster lets you release your software reliably, to any environment, at whatever pace the business demands.

Build a self-service release management platform by allowing different teams to manage their own applications and deploy to their own environments.

Start simple and then scale to thousands of servers and the cloud.

### Here is a Short Guide to get you Started Quick:

In this tutorial, you will learn how to deploy a simple ASP.NET web application along with its IIS configuration through a pipeline to create a repeatable release process.

<iframe width="600" height="337" src="https://www.youtube.com/embed/iwvlgZmp5Ug" frameborder="0" allowfullscreen="true"></iframe>

*View the full step-by-step tutorial [here](/support/tutorials/buildmaster/deployments/deploying-a-simple-web-app-to-iis).*

BuildMaster will install on any supported version of Windows; simply [download](/buildmaster/download), and click through the installer to get BuildMaster up and running in minutes. Through the installer, you select the edition you wish to install; a trial, the free edition, or enter a license key. Review the [step-by-step Installation Guide](/support/documentation/buildmaster/installation/windows-guide) for details as to what's happening behind the scenes.

### Agents and Servers in BuildMaster

To deploy releases to your servers, BuildMaster needs to be able to communicate with those servers over a fast and secure channel. There are a few options for doing this.

The Inedo Agent is generally the best way to communicate with a *Windows server*. It's a lightweight, highly-optimized agent that is simple to deploy and upgrade, but is also backwards- and forwards-compatible to minimize change risks. Alternatively, BuildMaster can use *PowerShell Remoting* to communicate with Windows servers; however, this is generally slower and less resilient than the Inedo Agent protocol.

To communicate with Linux servers, BuildMaster uses *SSH* and *SFTP*, and if you're using BuildMaster to interact with the server it is installed on, you can just set it up using a *local agent*.

:::circle-button-set
[Learn More About Agents & Servers](/support/documentation/buildmaster/administration/agents){.doc-button}
:::
