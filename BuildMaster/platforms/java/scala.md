---
title: Scala
subtitle: Building and Deploying Scala Applications
sequence: 800
show-headings-in-nav: true
---

- [Scala](https://www.scala-lang.org/) is a statically-typed programming language featuring both object-oriented and functional principles designed to run on the JVM or in a browser ([Scala JS](https://www.scala-js.org/))
- the language was designed to address some of the criticisms of Java

## Building Scala Applications {#building data-title="Building Scala Applications"}

- Scala is built similarly to Java
- because Scala code compiles to Java bytecode, any compiled Scala application can run on the Java Runtime Environment (JRE), or in other words, any platform that runs an application built from traditional Java source code
- `sbt`, short of "Scala Build Tool", is the typical build tool used to build Scala applications, and included with with common IDEs
- when a project is created following a the recommended directory structure, executing the following interactive CLI commands will build a Scala application:
```
$ sbt 
> clean 
> compile
```

## Deploying Scala Applications {#deployment data-title="Deploying Scala Applications"}

- Scala applications are packaged the same way as Java applications (e.g. JAR, WAR, or EAR files)
- this means they can be deployed in exactly the same way as any other Java application
- refer to the [Deploying Java Applications](/docs/buildmaster/platforms/java#deployment) documentation to see a list of methods used to deploy Scala applications

## Automation with BuildMaster {#buildmaster data-title="Automation with BuildMaster"}

- the automated process is the same as the manual process above, except that commands should be run in "batch mode" to prevent prompts for user input
- batch mode is executed by supplying arguments when invoking `sbt`, e.g. `sbt clean compile`
- example OtterScript plan:
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
