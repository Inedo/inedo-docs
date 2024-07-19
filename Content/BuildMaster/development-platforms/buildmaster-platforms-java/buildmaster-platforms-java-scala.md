---
title: "Scala"
order: 8
---


[Scala](https://www.scala-lang.org/) is a statically typed programming language featuring both object-oriented and functional principles. It is designed to run on the Java JVM or in a browser ([Scala JS](https://www.scala-js.org/)). It also addresses some of the criticisms of Java.

## Building Scala Applications 

Scala is similar in structure to Java. Since Scala code is compiled to Java bytecode, any compiled Scala application can run on the Java Runtime Environment (JRE), i.e., on any platform that runs an application built from traditional Java source code.

`sbt`, short for Scala Build Tool, is the typical tool used to build Scala applications and is included with common IDEs. When a project is created according to the recommended directory structure, executing the following interactive commands CLI will build a Scala application:
```
$ sbt 
> clean 
> compile
```

## Deploying Scala Applications 

Scala applications are packaged in the same way as Java applications (e.g., JAR, WAR, or EAR files). This means, they can be deployed in exactly the same way as any other Java application (refer to the [Deploying Java Applications](/docs/buildmaster/development-platforms/buildmaster-platforms-java#deployment) documentation to see a list of deployment methods).

## Automation with BuildMaster 

The automated process is the same as the manual process above, except that commands should be run in batch mode to prevent prompts for user input. Batch mode is executed by supplying arguments when invoking `sbt` (e.g., `sbt clean compile`).

Example OtterScript plan:
```
Git::Get-Source
(
    RepositoryUrl: https://github.com/Inedo/ProfitCalcScala.git,
    Branch: master
);

Exec
(
    FileName: sbt,
    Arguments: clean compile
);

Create-Artifact ProfitCalcScala
(
    From: ProfitCalcScala\target
);
```