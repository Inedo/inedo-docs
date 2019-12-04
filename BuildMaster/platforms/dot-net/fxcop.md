---
title: FxCop & FxCop Analyzers
sequence: 800
keywords: buildmaster, build-reports, fxcop
show-headings-in-nav: true
---
 
FxCop is a free static code analysis tool from Microsoft that checks .NET managed code assemblies for conformance to Microsoft's .NET Framework Design Guidelines. While technically speaking FxCop is [deprecated in favor of Roslyn Analyzers](https://docs.microsoft.com/en-us/visualstudio/code-quality/fxcop-analyzers?view=vs-2019), many projects still depend on it for code quality. 

## Running FxCop {#fxcop data-title="Running FxCop"}

FxCop is executed during a build in Visual Studio, and also by the `FxCopCmd.exe` utility that ships with Visual Studio Enterprise.

## Running FxCop in BuildMaster {#buildmaster data-title="FxCop in BuildMaster"}

Any configured Roslyn Analyzers will already automatically be executed during the build process as long as a project's NuGet packages are restored, and the project is compiled with MSBuild.

Alternatively, BuildMaster can execute FxCop in a build or deploy plan, then capture the output from the report into a [build report](/docs/buildmaster/builds/tests/build-reports). 
 
### Pre-requisites: 
{.docs} 
- [Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads) installed on the build server

Example plan: 
 
```
Exec "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Team Tools\Static Analysis Tools\FxCop\FxCopCmd.exe"
(
    Arguments: /out:report.xml 
);
 
Capture-FileReport
(
    Path: report.xml
);
```