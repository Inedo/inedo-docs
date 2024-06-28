---
title: "JUnit"
order: 7
---


[JUnit](https://junit.org/) is the most popular unit-testing framework for Java projects. Developers are able to run tests directly in most IDEs such as IntelliJ, Eclipse, and others. 

Within a CI build pipeline, tests can be run using common tasks/phases in build tools such as [Maven](https://maven.org/) and [Gradle](https://gradle.org/), or alternatively executed directly with [Java directly](https://junit.org/junit5/docs/current/user-guide/#running-tests-console-launcher-options): 

```
java -jar junit-platform-console-standalone-1.6.0.jar
```

## JUnit Testing in BuildMaster

BuildMaster's [built-in unit test reporting](/docs/buildmaster/builds-continuous-integration/automated-testing-verification/buildmaster-ci-cd-testing-and-verification-unit-tests) is designed to work with the JUnit test framework. 

To enable integration with JUnit:

 1. Ensure that the Java extension is installed
 2. A configuration variable named `$JavaPath` exists and is configured to point to the `java` executable file (`java.exe` on Windows)

An example of a build plan that retrieves the source code, compiles the source code with Maven, and runs JUnit tests is:

```
GitHub::Get-Source
(
    Organization: Inedo,
    Repository: ProfitCalcJava
);

Java::Execute-Maven
(
    GoalsAndPhases: "test-compile compile",
    In: ProfictCalcJava
);

Java::Execute-JUnit
(
    Includes: **.class,
    From: ProfictCalcJava\src\test
);
```

Note that this example may be slightly redundant because the default Maven lifecycle will execute tests in phase prior to package. Running the `Execute-JUnit` operation separately enables BuildMaster to record the output in its own format for reporting purposes.

## Test Result Behavior

Test results are logged in the build execution log and also in the *Unit Test* section of a build, with a more detailed breakdown and/or test history. If a unit test fails, the build is stopped by default.

If you want the desired behavior to continue regardless of the failure, wrap the operation in an OtterScript try/catch statement. For example:

```
try
{
    Java::Execute-JUnit
    (
        Includes: **.class,
        From: ProfictCalcJava\src\test
    );
}
catch
{
    Log-Warning Ignoring test failures for now...;
}
```

## Preventing Deployment of Builds with Failed Unit Tests 
To ensure that a build with failed unit tests is never deployed, add a Unit Tests Passed [automated check](/docs/buildmaster/deployment-continuous-delivery/automated-testing-verification-automatic-manual-approvals/buildmaster-ci-cd-testing-and-verification-approvals-automated-checks) to the pipeline stage immediately following the build stage (typically, Integration). This prevents a build with failed unit tests (and optionally inconclusive tests) from being promoted, regardless of whether the errors were logged in the build plan or ignored. Of course, this build can still be forced to the next phase, but this requires special administrative permissions.