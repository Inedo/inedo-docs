---
title: "Jetty"
order: 6
---


[Eclipse Jetty](https://www.eclipse.org/jetty/) is a free web server and javax.servlet container used to run Java web applications. Jetty has been part of the Eclipse Foundation since 2009; it is open-source and free for commercial use and distribution, as well as local development needs. It includes support for HTTP/2, WebSocket, OSGi, JMX, JNDI, JAAS and many other integrations. It can also be standalone or used to embed a webserver into devices, tools, frameworks, and more. While Jetty does *not* implement the entire Jakarta EE specification, most Java IDEs include templates (e.g., Maven archetypes) to create the necessary project structure to build and deploy a web applications in Jetty.

## Overview & Concepts

Jetty is built on Java and requires the Java JRE to run. It does not include separate Windows and Linux scripts; instead, you start the server directly from Java:

```
java -jar start.jar
```

Jetty does not include a Graphical User Interface (GUI), so you can only make changes via the command line. For example, you would make configuration changes by passing parameters to the `java -jar start.jar` command (i.e., `java -jar start.jar --add-to-start=https,http2`).

Jetty does not come with the components required to run as a Windows service, but you can use third-party tools to configure it. See Jetty's documentation for more information. Jetty *contains* scripts to run as a Linux/Unix service out of the box. You can read more in the Jetty documentation.


## Deploying Java Web Applications to Jetty

The standard way of deploying Jetty applications is copying a WAR file to `$JETTY_HOME\webapps` and letting Jetty's deployment scanner run and automatically deploy the file.

You can also customize application deployments in Jetty using the Jetty Deployable Descriptor XML File.

If you are using a non-standard web application structure or static files, you will need to set up a custom Deployable Descriptor File.

## Automation with BuildMaster

The process of deploying Jetty in BuildMaster follows the same pattern as the manual process.

Example OtterScript plan:
```
set $JETTY_HOME = $EnvironmentVariable(JETTY_HOME);

# the ProfitCalcJava artifact contains  org.inedo.profitcalc-1.2.3.war
Deploy-Artifact ProfitCalcJava
(
    To: $JETTY_HOME\webapps
);
```