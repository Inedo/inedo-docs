---
title: Deploying a Windows Service
sequence: 200
keywords: buildmaster, deployments, windows, service
---

A Windows Service is a program that runs in the background of the Windows OS environment, similar to a Unix daemon. In order to be considered a service, a program must adhere to the interfaces provided by the [Service Control Manager (SCM)](https://docs.microsoft.com/en-us/windows/win32/services/services). The SCM controls the following:

{.docs}
 - Database of installed services
 - Disk path of the programs/executables to run
 - Additional metadata for each service such as name, description, restart behavior, etc.

Services can be developed in .NET or in C/C++ using the Win32 API. Regardless of how they are developed, BuildMaster makes it easy to build and deploy them.

## Building Windows Services {#building data-title="Building Windows Serivces"}

Building a service application in BuildMaster is essentially the same as similar [Building .NET Console Applications in BuildMaster](/docs/buildmaster/builds/platform-specific/dot-net/console-app). The main difference is in how you deploy it.

## Deploying Windows Services {#deployment data-title="Deploying Windows Serivces"}

Windows Services are typically deployed in two stages. For new services, an entry in the SCM must be created, whether it's added manually by Operations personnel (via `sc.exe`, PowerShell, or other mechanism), or automated by a Configuration Management tool like [Otter](https://inedo.com/otter), Chef, Puppet, etc. For organizations who are not bound by operations restrictions, BuildMaster can just as easily be configured to provision these services as well.

Once the service entry exists on a server, service build artifacts may be repeatedly deployed as long as the service is stopped in the SCM prior to deployment. It is important to note that a Windows Service does not need to be deleted and recreated each time a new version of the service is deployed.

## Automating Windows Service Deployment with BuildMaster {#buildmaster data-title="Automation with BuildMaster"}

While there are various methods to deploy a Windows Service, we recommend deploying them using the following general pattern:

{.docs}
 - Stop service (`Windows::Stop-Service` operation) 
 - Deploy build artifact to the directory that contains the executable
 - Start service (`Windows::Start-Service` operation)

Using the operations is basically the same as running the following PowerShell commands: 

```
Stop-Service HDarsService
# deploy files...
Start-Service HDarsService
```

Additionally, there is an `Windows::Ensure-Service` operation that can be used. It has 2 minor differences from the above control operations:

{.docs}
 - the ensure operation is designed for Configuration Management (i.e. Otter)
 - it can be used to create new services or delete existing services
