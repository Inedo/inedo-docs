---
title: Tomcat
subtitle: Deploying Java Applications to Tomcat
sequence: 300
show-headings-in-nav: true
---

[Apache Tomcat](https://tomcat.apache.org/) is the most popular web server environment used to run Java web applications. 

The project includes open-source implementations of Jakarta EE functionality such as servlets, Java Server Pages (JSP), and more. When hosting your application using Tomcat, your Java web application's target output needs to generate a WAR file.  Most Java IDEs include templates (e.g., Maven archetypes) that create the necessary project structure to build and deploy a web application.

## Overview & Concepts {#overview data-title="Tomcat Overview"}

Tomcat runs on both Windows and Linux, and it includes certain functionality for both.

On Windows, Tomcat can run as a console application and is controllable by batch scripts (e.g., `%CATALINA_HOME%\bin\startup.bat`) or can be hosted as a Windows Service and controlled by the Windows Service Control Manager. 

On Linux, it can run as a console application and is controllable by shell scripts (e.g. `$CATALINA_HOME/bin/startup.sh`) or configured on a per-distro basis to run as a daemon where starting/stopping it depends on the `init` script from the package.

Tomcat internally consists of three major application service components:

{.docs}
 - **Catalina** - Java servlet container
 - **Coyote** - HTTP web server
 - **Jasper** - JSP engine

The `$CATALINA_HOME/conf/server.xml` is the configuration file for the initial startup port for the web server and individual `<Service>` elements that contain information about each hosted web application.

## Deploying Java Web Applications to Tomcat {#deployment data-title="Deployment"}

By default, applications are packaged as WAR files and deployed to the `$CATALINA_HOME\webapps` directory.  The general process involves:

{.docs}
  - stopping the Tomcat server: `$CATALINA_HOME/bin/shutdown.sh`
  - deploy WAR file to `$CATALINA_HOME\webapps` directory
  - starting the Tomcat server: `$CATALINA_HOME/bin/startup.sh`

## Automation with BuildMaster {#buildmaster data-title="Automation with BuildMaster"}

The process for automating Tomcat with BuildMaster follows the same pattern as the manual process.

Example OtterScript plan:
```
set $CATALINA_HOME = $EnvironmentVariable(CATALINA_HOME);

Exec $PathCombine($CATALINA_HOME, bin, shutdown.sh); # .bat on Windows

# the ProfitCalcJava artifact contains  org.inedo.profitcalc-1.2.3.war
Deploy-Artifact ProfitCalcJava
(
    To: $PathCombine($CATALINA_HOME, webapps)
);

Exec $PathCombine($CATALINA_HOME, bin, startup.sh); # .bat on Windows
```
