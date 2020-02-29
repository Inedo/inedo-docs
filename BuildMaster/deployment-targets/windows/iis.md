---
title: Deploying an IIS Website
sequence: 300
keywords: buildmaster, deployments, web, iis
show-headings-in-nav: true
---

Internet Information Services, commonly referred to as IIS, is Microsoft's web server technology. IIS is an optional installable component, and is typically used to host various kinds of web applications, from static HTML to ASP.NET.

An application served by IIS generally consists of 3 fundamental components:

{.docs}
 - **Site** – the logical unit of a website as a whole that contains configuration options for the home directory (i.e. where the web application files reside), security, logging, and more
 - **Application pool** – a worker process that serves requests that may be shared with other sites
 - **Binding** – the hostname and/or IP address used to access the site

IIS also supports [virtual directories](https://docs.microsoft.com/en-us/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) that are frequently used as a deployment target. A virtual directory is effectively a "subfolder" of a website that has a completely different home directory than its parent site.

## Building Sites for IIS {#building data-title="Building Sites for IIS"}

IIS supports a wide variety of development platforms, from static HTML to ASP.NET, and there are a lot of ways you can build applications for IIS in BuildMaster with these platforms.

See [Building an ASP.NET Web Application](/docs/buildmaster/builds/platform-specific/dot-net/asp-net), as well as [NodeJS](/docs/buildmaster/builds/platform-specific/node-js), [PHP](/docs/buildmaster/builds/platform-specific/php), and [Python](/docs/buildmaster/builds/platform-specific/python) for some examples.


## Deploying IIS Websites {#deployment data-title="Deploying IIS Websites"}

IIS websites are typically deployed in two stages. As a pre-requisite for new websites, the 3 fundamental components described earlier must exist on the server, whether installed manually by Operations personnel, or automated by a Configuration Management tool like [Otter](https://inedo.com/otter), Chef, Puppet, etc. For smaller organizations whose Dev and Ops teams aren't silo'ed in such a manner, BuildMaster can just as easily be configured to provision these sites as well.

Once the site infrastructure exists on a server, website build artifacts may be repeatedly deployed as long as:

{.docs}
 - The application pool is stopped to prevent conflicts (or an  `app_offline.htm` file is present in the home directory). 
 - The files are deployed to a different directory and the home directory is swapped at the site level (see [Blue/Green deployment](/docs/buildmaster/deployments/patterns/blue-green))

## Automating IIS Deployment with BuildMaster {#buildmaster data-title="Automation with BuildMaster"}

::: {.attention .best-practice}
You can view an example BuildMaster application that deploys to an IIS website by creating a new application and selecting the "ProfitCalc Tutorial" application.
:::

While there are various methods to deploy to IIS, we recommend deploying IIS applications using the following general pattern:

{.docs}
 - Stop application pool (`IIS::Stop-AppPool` operation) 
 - Deploy build artifact to home directory
 - Start application pool (`IIS::Start-AppPool` operation)

This group of operations can be performed on any server configured in BuildMaster. BuildMaster can interact with remote servers through [agents](/docs/buildmaster/administration/agents-and-infrastructure/servers). To specify which server to deploy the website to, use a `for server` block in the OtterScript plan, or as a best-practice, specify a target server in the [pipeline stage target](/docs/buildmaster/verification/pipelines#pipeline-stages).

An example BuildMaster plan that deploys a website to IIS on a remote server is:

```
for server us-west-web-01
{
    IIS::Stop-AppPool HDarsAppPool();
    Deploy-Artifact Website
    (
        To: D:\Websites\HDarsSite
    );
    IIS::Start-AppPool HDarsAppPool();
}
```

Using the operations is basically the same as running `appcmd.exe` in PowerShell with the following arguments: 

```
PS D:\Websites\HDarsSite> appcmd.exe stop apppool -name:"HDarsAppPool"
PS D:\Websites\HDarsSite> Expand-Archive -Path "E:\Artifacts\HdarsSite.zip"
PS D:\Websites\HDarsSite> appcmd.exe start apppool -name:"HDarsAppPool"
```

Additionally, there is an `IIS::Ensure-AppPool` operation that can be used. It has 2 minor differences from the above control operations:

{.docs}
 - the ensure operation is designed for Configuration Management (i.e. Otter)
 - the ensure operation does not "wait for completion" on control operations (i.e. if you stop an application pool, the ensure operation will not wait until it is fully stopped) 

