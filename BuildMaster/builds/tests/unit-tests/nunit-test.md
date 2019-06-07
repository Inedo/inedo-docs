---
title: NUnit
subtitle: Running unit tests with NUnit Runner
keywords: NUnit, unit test, unit testing, buildmaster
sequence: 100
---

BuildMaster is a fully-functional Continuous Integration tool for a variety of platforms allowing your organization to start using CI/CD today. That is true for Unit Testing Visual Studio projects with the NUnit Runner. You can use this in a build pipeline to run unit and functional tests.

#### What is NUnit Testing?
NUnit is an evolving, open source framework designed for writing and running tests in Microsoft .NET programming languages. NUnit, like JUnit, is an aspect of test-driven development (TDD). Tests can be run continuously. Results are provided immediately. Multiple tests can be run concurrently. No subjective human judgments or interpretations of test results are required

#### Why should you use NUnit with BuildMaster?
You can use this automation framework for both unit testing and UI testing.  You can set up buildmaster to require unit tests to pass before the deployment can go to the next stage. 


#### How to Run an NUnit Test In BuildMaster

In a BuildMaster build plan you'll need to be working with Visual Studio solution that contains the NUnit testing framwork. 

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

Execute NUnit Test

```
NUnit::Execute-TestProject
(
    TestFile: ~/Output/ProfitCalc.Test.dll,
    NUnitExePath: $NUnitConsolePath,
    OutputDirectory: ~/Results
);
```


#### Pass or Fail
If your tests all pass you will see the results in your execution logs.  If __any__ of the tests happen to fail the error will be logged and the rest of your plan will be skipped. You may also want to wrap this operation in a try/catch to handle the error more cleanly or to do more with the test results like notify any interested parties. 

#### Pipeline Promotion 
Add an _Automatic Gate Approval_ with the type of _Unit Tests_ to your integration stage in your pipeline to prevent promotion to a stage if a unit test fails. (The promotion can ultimately be manually forced if needed). This will ensure that if the application has automatically been promoted to a stage that requires gate approval, it has has passed all unit tests. 