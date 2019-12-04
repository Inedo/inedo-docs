---
title: Web Deploy Packages
sequence: 900
keywords: buildmaster, deployment, dot-net, web-deploy
show-headings-in-nav: true
---

[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (msdeploy) is tool developed by Microsoft that simplifies deployment of web applications and websites to IIS servers and enables administrators and delegated users to use IIS Manager to deploy ASP.NET and PHP applications to IIS. This is accomplished by creating a Web Deploy Package (either in Visual Studio or with the tool), and then deploying that package directly to the IIS Web Deploy Extension. Visual Studio also uses Web Deploy when doing a *One-Click publish* or *Right-Click Deploy* of a web application.

Although BuildMaster renders the Web Deploy technology largely obsolete (see [Deploying an IIS Website](/docs/buildmaster/deployments/targets/windows/iis) for a much simpler approach), there are a lot of reasons that an organization will still use Web Deploy:

{.docs}
  - legacy applications that are infrequently updated use this process, and aren't worth migrating away from Web Deploy
  - dev and release teams are silo'ed, and developers simply deliver Web Deploy packages to operations
  - Web Deploy packages are handed-off directly to clients

BuildMaster can be used to deploy Web Deploy packages without having to change existing development processes. Instead of the traditional method of deploying your web applications to a server, you "deploy" it to stakeholders or customers by sending them an email with a link to the package.

## Creating a Web Deploy Package {#create data-title="Creating a Web Deploy Package"}

By adding the `MSBuild::Build-Project` operation in your BuildMaster plan, you can package and publish a web application project as a Web Deploy package by following the example plan below:

```
MSBuild::Build-Project WebDeployProject.csproj
(
    Configuration: Debug,
    Platform: AnyCPU,
    Arguments: /t:Package /p:PackageLocation=WebDeployPackage.zip /p:PackageAsSingleFile=True
    To: ~\Output
);

Create-Artifact WebDeployPackage
(
    From: ~\Output
);
```

This plan effectively runs the `msbuild.exe` command, then captures the output into a BuildMaster build artifact:

```
msbuild WebDeployProject.csproj /t:Package /p:PackageLocation=WebDeployPackage.zip /p:PackageAsSingleFile=True
```

## Manual Deployment Notification {#notifications data-title="Ready to Deploy Notifications"}

After a Web Deploy package is created, BuildMaster users can be notified after it is created in order to deploy it. When you use the [Manual Operation](/docs/buildmaster/reference/operations/buildmaster/manual-operation) in your plan, the deployment plan will pause until a user logs in to BuildMaster and marks the task complete:

```
Perform-ManualOperation
(
    Name: Manually Deploy MyWebApp,
    AssignedTo: OpsTeamA,
    SendEmail: true
);
```

A "Manual Operation" is not required in order to do this, a simple email notification will work just as well, followed by a [user approval](/docs/buildmaster/verification/pipelines/approvals-and-gates/user-approvals) in a further stage of the pipeline.