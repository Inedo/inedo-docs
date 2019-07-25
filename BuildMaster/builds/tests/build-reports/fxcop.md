---
title: FxCop
subtitle: Running and Capturing FxCop Output
sequence: 100
keywords: buildmaster, build-reports, fxcop
---

### Pre-requisites: 
 - [Visual Studio Build Tools](https://visualstudio.microsoft.com/downloads) installed
 
FxCop is a free static code analysis tool from Microsoft that checks .NET managed code assemblies for conformance to Microsoft's .NET Framework Design Guidelines. While technically speaking FxCop is [deprecated in favor of Roslyn Analyzers](https://docs.microsoft.com/en-us/visualstudio/code-quality/fxcop-analyzers?view=vs-2019), many projects still depend on it for code quality. 
 
BuildMaster can execute FxCop in a build or deploy plan, then capture the output from the report into a [build report](/docs/buildmaster/builds/tests/build-reports).
 
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