---
title: VSTest (Visual Studio)
subtitle: Running NUnit, xUnit, MSTest, and more with VSTest Runner
keywords: unit test, buildmaster, vstest
sequence: 100
---

BuildMaster is a fully-functional Continuous Integration tool for a variety of platforms allowing your organization to start using CI/CD today. 
That is true for Unit Testing Visual Studio projects with the VSTest Runner. You can use this in a build pipeline to run unit and functional tests (Selenium, Appium, Coded UI test, and more). VSTest are test classes for .NET framework which are integrated into Visual Studio.

#### What Does VSTest Runner Work With?
The VSTest Runner in BuildMaster essentially runs the VSTest.Console.exe command line tool against a unit test container like MSTest-based tests, and any test frameworks that have a Visual Studio test adapter, such as xUnit, NUnit, Chutzpah.

#### How to a Unit Test with VSTest Runner

In a BuildMaster build plan you'll need to be working with Visual Studio solution that contains one of the test frameworks outlined above. 

Get your source code from a repository:

```
 GitHub::Get-Source
 (
     Organization: Inedo,
     Repository: ProfitCalc
 );
```    

Compile your source code
```
 NuGet::Restore-Packages();
 MSBuild::Build-Project ProfitCalc.sln
 (  
     To: ~\Output
 );
```

Execute the VSTest runner against your test framework container. 
```
 WindowsSdk::Execute-VSTest
 (
     TestContainer: ~\Output\ProfitCalc.Tests.dll
 );
```

#### Pass or Fail
If your tests all pass you will see the results in your execution logs.  If __any__ of the tests happen to fail the error will be logged and the rest of your plan will be skipped. You may also want to wrap this operation in a try/catch to handle the error more cleanly or to do more with the test results like notify any interested parties. 

#### Pipeline Promotion 
Add an _Automatic Gate Approval_ with the type of _Unit Tests_ to your integration stage in your pipeline to prevent promotion to a stage if a unit test fails. (The promotion can ultimately be manually forced if needed). This will ensure that if the application has automatically been promoted to a stage that requires gate approval, it has has passed all unit tests. 

#### More Options
If you are intested in getting more information on how the VSTest runner works with additional options like _rerunType_, _customBatchSizeValue_, _runSettingsFile_, and more - check out [this documentation](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/test/vstest?view=azure-devops)
