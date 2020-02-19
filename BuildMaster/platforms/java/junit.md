---
title: JUnit
subtitle: Running JUnit Tests
keywords: buildmaster, unit-testing, junit
sequence: 400
show-headings-in-nav: true
---

[JUnit](https://junit.org/) is the most popular unit testing framework for Java projects. Developers are able to run tests directly in most IDEs such as IntelliJ, Eclipse, and more. 

Within a CI build pipeline, tests can be run using common tasks/phases in build tools such as [Maven](https://maven.org/) and [Gradle](https://gradle.org/), or alternatively executed directly with [Java directly](https://junit.org/junit5/docs/current/user-guide/#running-tests-console-launcher-options): 

```
java -jar junit-platform-console-standalone-1.6.0.jar
```

## JUnit Testing in BuildMaster {#buildmaster data-title="JUnit with BuildMaster"}

BuildMaster's [built-in unit test reporting](/docs/buildmaster/ci-cd/testing-and-verification/unit-tests) is designed to work with the JUnit test framework. To enable integration with JUnit:

{.docs}
 1. Ensure the Java extension is installed
 2. A configuration variable named `$JavaPath` exists and is configured to point to the `java` executable (`java.exe` on Windows)

An example build plan that gets source, runs tests, and packages a JAR using Maven is as follows:

```
GitHub::Get-Source
(
    Organization: Inedo,
    Repository: ProfitCalcJava
);

Java::Execute-JUnit
(
    Includes: **.class,
    From: ProfictCalcJava\src\test
);

Java::Execute-Maven
(
    GoalsAndPhases: "clean package",
    In: ProfictCalcJava
);
```

Note that this example may be slightly redundant because the default Maven lifecycle will execute tests in phase prior to "package". Running the `Execute-JUnit` operation separately enables BuildMaster to record the output in its own format for reporting purposes.

## Test Result Behavior {#results data-title="Test Result Behavior"}

Test results are logged in the build execution log, and also to the *Unit Test* section of a build, with a more specific breakdown and/or test history. If a unit test fails, the default behavior is to halt the build. If the desired behavior is the continue regardless of failure, wrap the operation in an OtterScript try/catch statement, for example:

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

## Preventing Deployment of Builds with Failed Unit Tests {#preventing-deployment data-title="Prevent Deploying Untested Builds"}

To ensure that a build with failed unit tests is never deployed, add a "Unit Tests Passed" [automated check](/docs/buildmaster/verification/pipelines/approvals-and-gates/automated-checks) to the pipeline stage immediately following the build stage (this is typically integration). This will prevent a build with failed unit tests (and optionally inconclusive tests) from being promoted regardless if errors were logged or ignored in the build plan. Of course, this build can still be forced to the next stage, but special administrative permissions are required in order to do so.
