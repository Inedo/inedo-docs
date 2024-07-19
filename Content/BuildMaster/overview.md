---
title: "Getting Started with BuildMaster"
order: 1
---

BuildMaster is a self-hosted, cross-platform CI/CD tool that helps all team members build and deploy their modern and legacy applications, both on on-premise servers and in the cloud. Developers, testers, operation engineers, and managers alike can use BuildMaster to find the information they need, review and approve builds, and deploy using self-service, prompted input.

You can configure all of this with relative ease, as BuildMaster includes visual editors, guides and best practices for CI/CD. There are also script templates supported by a powerful scripting language (OtterScript) that can also be edited in a low-code visual mode.

Whether it's the first application or the hundredth application, BuildMaster guides users through selecting the repositories, projects, tools, pipelines, and deployment targets needed to automate build and deployment for their applications.

## How is BuildMaster Used?
There are two main ways in which BuildMaster users configure applications:

* **Build & Deploy (full CI/CD)** from Git, Subversion, or other source control
* **Import Artifacts & Deploy** from Jenkins, TeamCity and other CI servers

This allows you to easily migrate from or integrate with your existing CI server tools while using BuildMaster's release management, deployment pipelines, approval processes, and other capabilities.

Regardless of how you configure your applications, BuildMaster has GUI and automation features that let you:
* Browse Git branches and commits from repositories to create builds from that code
* View projects and builds from your CI server to import their artifacts
* Monitor changes to automate the creation or import of builds

:::(Internal) (MAYBETODO)
Perhaps talk and link about these features?
  - Application
  - Builds & Deployments
  - Pipelines, Scripts, & Script Templates
:::

## How to Get Started with BuildMaster?
BuildMaster is self-managed, which means you have the option to run BuildMaster on-premises or in your private/public cloud. Both Windows and Linux are supported. Check out the [BuildMaster Installation Guide](/docs/buildmaster/installation-maintenance/buildmaster-installation-guide) to learn how to get BuildMaster up and running in just a few minutes.

:::(Info) (👨‍🏫 Quick Start: ASP.NET MVC Application)
For a demonstration of creating an application in BuildMaster, read our guide on [building and deploying an ASP.NET MVC Application (CI/CD)](/docs/buildmaster/modeling-your-applications/buildmaster-applications-concepts/buildmaster-howto-build-and-deploy-an-aspnet-mvc-application). 

![buildmaster-oobe-first-app](/resources/docs/buildmaster-oobe-first-app.png){height="" width=""}

[This guide](/docs/buildmaster/modeling-your-applications/buildmaster-applications-concepts/buildmaster-howto-build-and-deploy-an-aspnet-mvc-application) will show you how BuildMaster guides you through configuring an application.

📺 A 3-minute Video:  Getting Started with BuildMaster
<iframe width="600" height="337" src="https://www.youtube.com/embed/Byi7gnOn6Hc?si=1EXwRRGjhsvvt-9T" frameborder="0" allowfullscreen="true"></iframe>
:::

BuildMaster also includes a number of sample applications that can help you learn how to configure different technologies or use advanced CI/CD techniques.

![buildmaster-oobe-sample-apps](/resources/docs/buildmaster-oobe-sample-apps.png){height="" width="50%"}

## Agents & Servers in BuildMaster

To deploy releases to your servers, BuildMaster needs to be able to communicate with those servers over a fast and secure channel. There are a few ways to do this.

The Inedo Agent is generally the best way to communicate with a *Windows Server*. It is a lightweight, highly optimized agent that is easy to deploy and update, but is also backward and forward-compatible to minimize change risks. Alternatively, BuildMaster can also use *PowerShell Remoting* to communicate with Windows Servers; however, this is generally slower and less resilient than the Inedo Agent protocol.

For communication with Linux servers, BuildMaster uses *SSH* and *SFTP*, and if you are using BuildMaster to interact with the server it is installed on, you can simply set up a *local agent*.

[Learn More About Agents & Servers](/docs/installation/agents-and-servers)