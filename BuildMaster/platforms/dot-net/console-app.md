---
title: Console Applications
subtitle: Building .NET Console Applications
sequence: 100 
keywords: buildmaster, msbuild, dot-net
show-headings-in-nav: true
---

Console applications are designed to be used from a "text-only" interface, or run without any interface by another automated tool. The functionality/behavior of a console application is controlled by its arguments, and applications can range in complexity from a simple "hello world" application, to a cornerstone of a platform (e.g. Git), or even monoliths.

Several technology platforms are capable of producing console applications, including .NET. End users are generally technical and console apps are typically used on desktops or servers, either manually or as part of a script.

In terms of distribution within an organization, locating these console applications can be a challenge, and is often performed manually in one of the following ways:

{.docs}
 - sent via email 
 - shared via Slack
 - stored on a file share
 - published to an internal website
 
These options make keeping track of console application releases tricky, particularly when they are used often by many folks throughout an organization.

## Building .NET Console Applications {#building data-title="Build Process"}

In general, the method for building console applications is platform specific. For .NET specifically, a developer would use Visual Studio, `dotnet build` for .NET Core applications, or in the case of automated tooling for example, a CI server would use MSBuild.

#### Example MSBuild Command

```
MSBuild.exe "ProfitCalc.Console.csproj" "/p:Configuration=Release" "/p:OutDir=C:\tmp\build-output\\"
```

After this command is run, `ProfitCalc.Console.exe` would be available in `C:\tmp\build-output`
	 
## Automation with BuildMaster {#buildmaster data-title="Automation with BuildMaster"}

At a high level, the process in BuildMaster follows the general pattern:

{.docs}
 - Get source code from the source control repository
 - Compile project with MSBuild
 - Capture artifact for deployment
		
### Compiling a Console Application {#compilation data-title="Compilation"}
	
To build a .NET console application, the first step is to acquire the source code from your SCM of choice, e.g. using `Git::Get-Source`. Once the source code is obtained, simply run the `MSBuild::Build-Project` operation:

```
MSBuild::Build-Project ~\Source\ProfitCalc.Console\ProfitCalc.Console.csproj
(
    To: ~\Output
);
```

While this works for the standard .NET framework, compiling [.NET Core applications](core) involves a different set of steps.

### Testing the Build {#testing data-title="Testing"}

Once your project or solution is compiled, BuildMaster can run [unit tests](/docs/buildmaster/builds/tests) or other automated tests against the build using [VSTest](/docs/buildmaster/builds/tests/unit-tests/vstest-runner), [NUnit](/docs/buildmaster/builds/tests/unit-tests/nunit-test), or any other testing framework.

### Capturing the Build Output for Deployment {#artifacts data-title="Capturing Build Output"}

After compilation of a console application is compelted, the output is usually an .exe file on Windows. This .exe file can be captured into a file for future deployment using the `Create-Artifact` operation.

To see an example of building a .NET console application in BuildMaster, create a new application using the "ProfitCalc Tutorial" sample application. Creating a new build of this application will compile the source code from https://github.com/Inedo/ProfitCalc.git, and captures a runnable executable into a [build artifact](/docs/buildmaster/builds/packaging/artifacts).

### Deploying Console Applications {#deployment data-title="Deploying Console Applications"}

Once the console application artifact is captured, it can be delivered in a variety of ways:

{.docs}
 - deployed to to a network drive
 - uploaded to a web site with a download link
 - emailed to users as an attachment 

An example of deploying to a network drive would be:

```
Deploy-Artifact ProfitCalc
(
    To: \\hdars-share-us-east\$d\apps\$ApplicationName\$ReleaseNumber
);
```
