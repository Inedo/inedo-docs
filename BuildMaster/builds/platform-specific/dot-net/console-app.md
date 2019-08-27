---
title: .NET Console Apps
subtitle: Building a .NET console application in BuildMaster
Docs URL: /builds/continuous-integration/azure-devops-tfs
sequence: 100 
keywords: buildmaster, dot-net, console-app, msbuild
---

### Pre-requisites: 

{.docs}
 - [Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads) installed

You can use BuildMaster to build an app written in any language. That includes .NET Console apps. BuildMaster allows you to build, test and deploy applications of any type using our intuitive interface or our integrated scripting language. 

By adding the _Build MSBuild Project_ operation to your plan you can build your .NET console application for any configuration (degug, release, etc) or platform (x86, x64, Win32, etc). 

Sample Plan:
```
MSBuild::Build-Project <pathToProject>\<projectName>.csproj
(
    Configuration: Debug,
    Platform: AnyCPU
);
```

This plan effectively runs this "msbuild.exe" command
```
msbuild <pathToProject>\<projectName>.csproj /p:Configuration=Debug;Platform=AnyCPU"
```


#### Additional Options
You can further customize this operation by specifying values for these additional options:

- Target directory: _Full path where the console app will be built_
- MSBuild properties: _Additional properties to pass to MSBuild, formatted as key=value pairs._
- Additional arguments: [MSBuild command-line reference](https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild-command-line-reference?view=vs-2019) 
- MSBuild tools path: _Full path to the MSBuild Tools_


Now you're ready to further configure your plan to run tests, create artifacts, or even deploy to any on-premises or cloud server. 

- [Running Tests](/docs/buildmaster/core-concepts/builds-and-ci/unit-tests)
- [Create Artifacts](/docs/buildmaster/builds/create-artifact)
- [OtterScript Overview](/docs/buildmaster/execution-engine/overview)
