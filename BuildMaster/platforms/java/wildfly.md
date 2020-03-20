---
title: WildFly
subtitle: Deploying Java Applications to WildFly/JBoss EAP
sequence: 400
show-headings-in-nav: true
---

[WildFly](https://wildfly.org), formerly JBoss Application Server, is a free application runtime used to run Java web applications. Built on WildFly, [JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) is a paid enterprise application runtime that includes open-source implementations of Jakarta EE functionality (e.g., servlets, Java Server Pages (JSP), and more). Both WildFly and JBoss EAP are commonly referred to as JBoss. 

As with other Java application servers, your Java web application's target output needs to generate a WAR file. Most Java IDEs include templates (e.g., Maven archetypes) that create the necessary project structure to build and deploy a web application. JBoss refers to these WAR files as "modules."

## Overview & Concepts {#overview data-title="WildFly Overview"}

WildFly can be run in `standalone` or `domain` mode. The features are the same for both; the only difference is server management.

{.docs}
- On Windows, WildFly can run as a console application and is controllable by batch scripts (e.g. `%JBOSS_HOME%\bin\standalone.bat` or `%JBOSS_HOME%\bin\domain.bat`) or can be hosted as a Windows Service and controlled by the Windows Service Control Manager
- On Linux, it can run as a console application and is controllable by shell scripts (e.g. `$JBOSS_HOME/bin/standalone.sh` or `$JBOSS_HOME/bin/domain.sh`) or configured on a per-distro basis to run as a daemon where starting/stopping it depends on the `init` script from the package.

You can customize what modules and configurations are used by the server by modifying the configuration files found in `%JBOSS_HOME%\standalone\configuration` or `%JBOSS_HOME%\domain\configuration`. You can also pass a configuration file as a parameter to the startup script:

{.docs}
- Standalone: `--server-config`
- Domain: `--domain-config`

WildFly includes a web-based management console out-of-the-box, but it can also be maintained via the [command line](https://docs.jboss.org/author/display/WFLY10/Admin+Guide#AdminGuide-CommandLineInterface).

## Deploying Java Web Applications to WildFly {#deployment data-title="Deployment"}

To deploy applications to WildFly, you'll typically use the Web Admin Console or the Admin Console CLI. To deploy to using the CLI, you would first connect to the WildFly server, then run the deploy command:

```
%JBOSS_HOME%\bin\jboss-cli.bat --connect 192.168.0.100 --commands="deploy ProfitCalcJava.ear"
```

You also have the option to specify a server list with the `--server-groups` parameter.

## Automation with BuildMaster {#buildmaster data-title="Automation with BuildMaster"}

Automating WildFly with BuildMaster follows the same pattern as the manual process.

Example OtterScript plan:
```
set $JBOSS_HOME = $EnvironmentVariable(JBOSS_HOME);

# the ProfitCalcJava artifact contains  org.inedo.profitcalc-1.2.3.war
Deploy-Artifact ProfitCalcJava
(
    To: $WorkingDirectory
);

Exec
{
    Path: PathCombine($JBOSS_HOME, bin, jboss-cli.sh)
    Arguments: --connect 192.168.0.100 --commands="deploy org.inedo.profitcalc-1.2.3.war"
}; # .bat on Windows
```
