---
title: "Running NUnit, xUnit, MSTest, and more with VSTest Runner"
order: 8
---


[Visual Studio Testing Tools](https://docs.microsoft.com/en-us/visualstudio/test/improve-code-quality) is the development framework centered around unit testing, code coverage, and load testing. Developers are able to run tests directly in Visual Studio using the Test Explorer window, and their unit testing framework of choice: NUnit, xUnit, MSTest, and more.

Within a CI build pipeline, tests can be executed with [`VSTest.Console.exe`](https://docs.microsoft.com/en-us/visualstudio/test/vstest-console-options), allowing organizations to run unit and functional tests (Selenium, Appium, Coded UI, etc.) as early as possible in the CI/CD process.

## Running VSTest with BuildMaster 

The VSTest Runner in BuildMaster essentially runs the command line tool `VSTest.Console.exe` against a unit test container such as MSTest-based tests and any test frameworks that have a Visual Studio test adapter, such as xUnit, NUnit, Chutzpah. These adapters are referenced via NuGet packages, which must be restored before MSBuild is executed.

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

WindowsSdk::Execute-VSTest
(
    TestContainer: ~\Output\ProfitCalc.Tests.dll
);
```

## Test Result Behavior

Test results are logged in the build execution log, and also to the *Unit Test* section of a build with a more specific breakdown and/or test history. If a unit test fails, the default behavior is to halt the build. If the desired behavior is to continue regardless of failure, wrap the operation in an OtterScript try/catch statement, for example:

```
try
{
    WindowsSdk::Execute-VSTest
    (
        TestContainer: ~\Output\ProfitCalc.Tests.dll
    );
}
catch
{
    Log-Warning Ignoring test failures for now...;
}
```

## Preventing Deployment of Builds with Failed Unit Tests 

To ensure that a build with failed unit tests is never deployed, add a Unit Tests Passed [automated check](/docs/buildmaster/deployment-continuous-delivery/automated-testing-verification-automatic-manual-approvals/buildmaster-ci-cd-testing-and-verification-approvals-automated-checks) to the pipeline stage immediately following the build stage (typically, Integration). This will prevent a build with failed unit tests (and optionally inconclusive tests) from being promoted, regardless whether errors were logged or ignored in the build plan. Of course, the build can still be forced to the next stage, but special administrative permissions are required in order to do so.