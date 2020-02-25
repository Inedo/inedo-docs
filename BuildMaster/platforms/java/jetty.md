---
title: Jetty
subtitle: Deploying Java Applications to Jetty
sequence: 500
show-headings-in-nav: true
---

- [Eclipse Jetty](https://www.eclipse.org/jetty/) is a free web server and javax.servlet container used to run Java web applications
- It includes support for HTTP/2, WebSocket, OSGi, JMX, JNDI, JAAS and many other integrations
- Jetty is open source and is free for commercial use and distribution as well as local development needs
- Jetty can be stand-alone or used to embed a webserver into devices, tools, frameworks, and more
- Jetty is part of the eclipse foundation since 2009
- Jetty does not implement the entire Jakarta EE specification
- most Java IDEs include templates (e.g. Maven archetypes) that create the necessary project structure to build and deploy a web applications in Jetty

## Overview & Concepts {#overview data-title="Jetty Overview"}

- Jetty is built on java and requires the Java JRE to run.  
- It does not include separate windows and linux scripts, instead you start the server directly from java

```
java -jar start.jar
```

- Jetty does not include a Graphical USer Interface (GUI), you can only make changes via teh command line
- You would make configuration changes by passing parameters to the `java -jar start.jar` command, (i.e. `java -jar start.jar --add-to-start=https,http2`)
- Jetty does not come out of the box with the components to run as a Windows Service, but you can use third party tools to configure it. View [Jetty's documentation for more information.](https://www.eclipse.org/jetty/documentation/9.4.26.v20200117/startup.html)
- Jetty does include scritps to run as a Linux/Unix service out of the box View [Jetty's documentation for more information.](https://www.eclipse.org/jetty/documentation/9.4.26.v20200117/startup.html)


## Deploying Java Web Applications to Jetty {#deployment data-title="Deployment"}

- The standard way of deploying applications is copying the war file to `$JETTY_HOME\webapps` and letting Jetty's deployment scanner scan and automatically deploy the file
- You can also customize application deployments in Jetty using the [Jetty Deployable Descriptor XML File](https://www.eclipse.org/jetty/documentation/9.4.26.v20200117/configuring-specific-webapp-deployment.html)
- If you are using a non-standard web application structure or static files, you will need to setup a custom Deployable Descriptor File

## Automation with BuildMaster {#buildmaster data-title="Automation with BuildMaster"}

- the process follows the same pattern as the manual process
- example OtterScript plan:
```
set $JETTY_HOME = $EnvironmentVariable(JETTY_HOME);

# the ProfitCalcJava artifact contains  org.inedo.profitcalc-1.2.3.war
Deploy-Artifact ProfitCalcJava
(
    To: $JETTY_HOME\webapps
);
```
