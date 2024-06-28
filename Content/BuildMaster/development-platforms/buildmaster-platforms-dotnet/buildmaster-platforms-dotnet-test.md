---
title: "Running Automated Tests"
order: 5
---


There are several different testing frameworks in .NET, including as NUnit, xUnit, MSTest, and Selenium. Regardless of which your team uses, BuildMaster can run these tests in your build script and record the detailed results for later review and analysis.

This article will show you how to add this step to your .NET build scripts, compare different test runners you can use, and explain how it works behind the scenes so that you can adjust your scripts as needed.

## Running your .NET Tests
The easiest way to run automated .NET tests is by selecting an option for "Run test project" on the the [Build.NET Project Script Template](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts/buildmaster-build-net-project-script-template) or [Build NuGet Package Script Template](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts/build-nuget-package-script-template). 

![](/resources/docs/buildmaster-dotnet-build-with-npm-template.png){width="50%"}

When you select a test project, the script template will simply adds a `DotNet::Test` operation to your build script. This operation will then run the [`dotnet test`](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-test) command-line tool and process the output.

### .NET Testing Tools & OtterScript
:::(Info)
Although `dotnet test` is not the only command-line tool that can execute tests for .NET projects, it is strongly preferred by Microsoft and the .NET development community as a whole. You should update your testing projects to use NuGet packages for the corresponding test framework so that you can use `dotnet test` for your projects.
:::

BuildMaster supports three different command-line tools that can run .NET tests:

| Tool | OtterScript Operation | Notes
| --- | --- | ---
| `dotnet`  | `DotNet::Test` | <span style="color:#090">✔ Recommended</span>, and works with nearly all testing frameworks
| `VSTest.Console.exe` | `WindowsSdk::Execute-VSTest` |  <span style="color:#C90">⚠ Not Recommended</span>, but may be required for to run tests on older projects
| `nunit-console.exe` | `NUnit::Execute-TestProject` | <span style="color:#C00">✖ Avoid if Possible</span>, but some really ancient projects will require this

#### Using VSTest Runner (Not Recommended)

VSTest Runner (i.e. `VSTest.Console.exe`) is a part of [Visual Studio Testing Tools](https://docs.microsoft.com/en-us/visualstudio/test/improve-code-quality), and generally requires that Visual Studio is installed. This is one of the reasons it's not recommended any more.

Before using the `WindowsSdk::Execute-VSTest` operation, you'll need to build your project using an operation like `DotNet::Build` or `MSBuild:Build-Project` and then specify the test container (e.g.  the `.dll` of a compiled testing project) to run. 

```(OtterScript)
WindowsSdk::Execute-VSTest
(
    TestContainer: ~\Output\ProfitCalc.Tests.dll
);
```

#### Using NUnit Console Runner (Not Recommended)
The [NUnit Console Runner](https://docs.nunit.org/articles/nunit/running-tests/Console-Runner.html) (i.e. `nunit-console.exe`) is a stand-alone command-line tool that can run NUnit tests. The NUnit project no longer recommends using it.

Before using the `NUnit::Execute-TestProject` operation, you'll need to build your project using an operation like `DotNet::Build` or `MSBuild:Build-Project` and then specify the test file (e.g.  the `.dll` of a compiled testing project) to run. 

```(OtterScript)
NUnit::Execute-TestProject
(
    TestFile: ~\Output\ProfitCalc.Test.dll
);
```

This operation depends on a configuration variable (`$NUnitConsolePath`), which defaults to `nunit-console.exe` (i.e., having the executable in the PATH), but may be overridden at the operation or server scope.


### Sample OtterScript
An example build plan that gets source and runs tests is as follows:

```(OtterScript)
GitHub::Get-Source
(
    Organization: Inedo,
    Repository: ProfitCalc
);
 
NuGet::Restore-Packages();

DotNet::Build ProfitCalc.sln
(  
    To: ~\Output
);

DotNet::Test
(
    Project: ProfitCalc.Tests.csproj
);
```

## Test Result Behavior

Test results are logged in the build execution log, and also to the "Tests" tab of a build with a more specific breakdown and/or test history. 

![buildmaster-test-results](/resources/docs/buildmaster-test-results.png){height="" width="75%"}


### Continuing after Test Failure
If a test fails, the default behavior is to halt the build. If the desired behavior is to continue regardless of failure, wrap the operation in an OtterScript try/catch statement, for example:

```(OtterScript)
try
{
    DotNet::Test
    (
        Project: ProfitCalc.Tests.csproj
    );
}
catch
{
    Log-Warning Ignoring test failures for now...;
}
```

## Blocking Deployment of Builds with Failed Unit Tests 

To ensure that a build with failed unit tests is never deployed, add a Unit Tests Passed [automated check](/docs/buildmaster/deployment-continuous-delivery/automated-testing-verification-automatic-manual-approvals/buildmaster-ci-cd-testing-and-verification-approvals-automated-checks) to the pipeline stage immediately following the build stage (typically, Integration). 

This will prevent a build with failed unit tests (and optionally inconclusive tests) from being promoted, regardless whether errors were logged or ignored in the build plan. Of course, the build can still be forced to the next stage, but special administrative permissions are required in order to do so.