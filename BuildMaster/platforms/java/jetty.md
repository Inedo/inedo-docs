---
title: Jetty
subtitle: Deploying Java Applications to Jetty
sequence: 500
show-headings-in-nav: true
---

[Eclipse Jetty](https://www.eclipse.org/jetty/) is a free web server and javax.servlet container used to run Java web applications that has been part of the eclipse foundation since 2009. Jetty is open-source and is free for commercial use and distribution as well as local development needs. It includes support for HTTP/2, WebSocket, OSGi, JMX, JNDI, JAAS and many other integrations and can be stand-alone or used to embed a webserver into devices, tools, frameworks, and more. While Jetty does *not* implement the entire Jakarta EE specification, most Java IDEs include templates (e.g., Maven archetypes) that create the necessary project structure to build and deploy a web applications in Jetty.

## Overview & Concepts {#overview data-title="Jetty Overview"}

Jetty is built on Java and requires the Java JRE to run. It does not include separate Windows and Linux scripts; instead, you start the server directly from Java:

```
java -jar start.jar
```

Jetty does not include a Graphical User Interface (GUI), so you can only make changes via the command line. For example, you would make configuration changes by passing parameters to the `java -jar start.jar` command, (i.e., `java -jar start.jar --add-to-start=https,http2`).

It also does not come out of the box with the components to run as a Windows Service, but you can use third-party tools to configure it. You can view [Jetty's documentation](https://www.eclipse.org/jetty/documentation/9.4.26.v20200117/startup.html) for more information. Jetty *does* include scripts to run as a Linux/Unix service out of the box. You can learn more by viewing [Jetty's documentation](https://www.eclipse.org/jetty/documentation/9.4.26.v20200117/startup.html).


## Deploying Java Web Applications to Jetty {#deployment data-title="Deployment"}

The standard way of deploying [[ATTN: RICH - make sure I didn't screw this up]] Jetty applications is copying the WAR file to `$JETTY_HOME\webapps` and letting Jetty's deployment scanner scan and automatically deploy the file.

You can also customize application deployments in Jetty using the [Jetty Deployable Descriptor XML File](https://www.eclipse.org/jetty/documentation/9.4.26.v20200117/configuring-specific-webapp-deployment.html)

If you are using a non-standard web application structure or static files, you will need to set up a custom Deployable Descriptor File.

## Automation with BuildMaster {#buildmaster data-title="Automation with BuildMaster"}

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
