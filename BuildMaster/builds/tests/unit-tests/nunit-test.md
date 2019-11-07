---
title: NUnit
subtitle: Running Unit Tests with NUnit Runner
keywords: buildmaster, unit-tests, nunit
sequence: 100
show-headings-in-nav: true
---

[NUnit](https://nunit.org/) is an evolving, open source framework designed for writing and running tests in Microsoft .NET programming languages.

## Running NUnit Tests {#nunit data-title="Running Tests"}

NUnit tests can be executed either in Visual Studio directly and viewed in the Test Explorer, or for automated runs used by CI tools, from the command line using the [`nunit-console.exe` CLI tool](https://github.com/nunit/docs/wiki/Console-Runner). The Visual Studio version requires projects to install both the [NUnit Framework](https://www.nuget.org/packages/NUnit/) and [NUnit Test Adapter](https://www.nuget.org/packages/NUnit3TestAdapter) NuGet packages in order to be run in the Test Explorer.

## Run NUnit Tests with BuildMaster {#buildmaster data-title="NUnit with BuildMaster"}

The NUnit Runner in BuildMaster essentially runs the `nunit-console.exe` command line tool against a unit test container. A tests container must reference the [NUnit Framework](https://www.nuget.org/packages/NUnit/) and [NUnit Test Adapter](https://www.nuget.org/packages/NUnit3TestAdapter) NuGet packages, and these packages must be restored prior to running MSBuild.

An example build plan that gets source and runs tests is as follows:

```
GitHub::Get-Source
(
    Organization: Inedo,
    Repository: ProfitCalc
);

NuGet::Restore-Packages();

MSBuild::Build-Project ProfitCalc.sln
(  
    To: ~\Output
);

NUnit::Execute-TestProject
(
    TestFile: ~\Output\ProfitCalc.Test.dll,
    OutputDirectory: ~\Results
);
```

The `NUnit::Execute-TestProject` depends on a configuration variable `$NUnitConsolePath`, which defaults to `nunit-console.exe` (i.e. having the executable in the PATH), but may be overridden at the operation or server scope.

## Test Result Behavior {#results data-title="Test Result Behavior"}

Test results are logged in the build execution log, and also to the *Unit Test* section of a build, with a more specific breakdown and/or test history. If a unit test fails, the default behavior is to halt the build. If the desired behavior is the continue regardless of failure, wrap the operation in an OtterScript try/catch statement, for example:

```
try
{
    NUnit::Execute-TestProject
    (
        TestFile: ~\Output\ProfitCalc.Test.dll,
        OutputDirectory: ~\Results
    );
}
catch
{
    Log-Warning Ignoring test failures for now...;
}
```

## Preventing Deployment of Builds with Failed Unit Tests {#preventing-deployment data-title="Prevent Deploying Untested Builds"}

To ensure that a build with failed unit tests is never deployed, add a "Unit Tests Passed" [automated check](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks) to the pipeline stage immediately following the build stage (this is typically integration). This will prevent a build with failed unit tests (and optionally inconclusive tests) from being promoted regardless if errors were logged or ignored in the build plan. Of course, this build can still be forced to the next stage, but special administrative permissions are required in order to do so.