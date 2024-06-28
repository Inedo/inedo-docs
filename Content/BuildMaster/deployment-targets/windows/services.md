---
title: "Deploying a Windows Service"
order: 1
---

A Windows Service is a program that runs in the background of the Windows environment OS, much like a Unix daemon. To be considered a service, a program must comply with the interfaces provided by the [Service Control Manager (SCM)](https://docs.microsoft.com/en-us/windows/win32/services/services). The SCM controls:


 - Database of installed services
 - Disk path of the programs/executables to run
 - Additional metadata for each service such as name, description, restart behavior, etc.

Services can be developed in .NET or in C/C++ using the Win32 API. Regardless of how they are developed, BuildMaster makes it easy to build and deploy these.

## Building Windows Services 

Building a service application in BuildMaster is essentially the same as [Building .NET Console Applications in BuildMaster](/docs/buildmaster/development-platforms/buildmaster-platforms-dotnet/buildmaster-platforms-dot-net-console-app). The main difference is in how you deploy it.

## Deploying Windows Services

Windows Services are typically deployed in two stages. For new services, an entry in the SCM must be created, whether it's added manually by Operations personnel (via `sc.exe`, PowerShell, or other mechanism), or automated by a Configuration Management tool like [Otter](https://inedo.com/otter), Chef, Puppet, etc. For organizations not bound by operations restrictions, BuildMaster can just as easily be configured to provision these services as well.

Once the service entry is present on a server, service build artifacts can be deployed repeatedly as long as the service is paused in the SCM before deployment. It is important to note that a Windows Service does not need to be deleted and rebuilt each time a new version of the service is deployed.

## Automating Windows Service Deployment with BuildMaster

While there are various methods for deploying a Windows Service, we recommend deploying them according to the following general pattern:

 - Stop service (`Windows::Stop-Service` operation) 
 - Deploy build artifact to the directory that contains the executable
 - Start service (`Windows::Start-Service` operation)

This group of operations can be performed on any server configured in BuildMaster. BuildMaster can interact with remote servers through [agents](/docs/buildmaster/administration-agents-and-infrastructure/buildmaster-servers). To specify which server to deploy the service to, use a `for server` block in the OtterScript plan, or as a best practice, specify a target server in the [pipeline stage target](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines#pipeline-stages).

An example BuildMaster plan that deploys a service on a server is:

```
for server us-east-svc-01
{
    Windows::Stop-Service HDarsService();
    Deploy-Artifact Service
    (
        To: D:\Services\HDarsService
    );
    Windows::Start-Service HDarsService();
}
```

Using the operations is basically the same as running the following PowerShell commands: 

```
PS D:\Services\HDarsService> Stop-Service HDarsService
PS D:\Services\HDarsService> Expand-Archive -Path "E:\Artifacts\HdarsService.zip"
PS D:\Services\HDarsService> Start-Service HDarsService
```

Additionally, there is a `Windows::Ensure-Service` operation that can be used. It has two minor differences from the above control operations:

 - the ensure operation is designed for Configuration Management (i.e., Otter)
 - it can be used to create new services or delete existing services