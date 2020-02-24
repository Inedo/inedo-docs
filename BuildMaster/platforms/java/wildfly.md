---
title: Wildlfy
subtitle: Deploying Java Applications to WildFly/JBoss EAP
sequence: 400
show-headings-in-nav: true
---

- [WildFly](https://wildfly.org), formerly JBoss Application Server, is a free application runtime used to run Java web applications
- [JBoss Enterprise Application Platform (EAP)] is a paid enterprise application runtime built on WildFly
- the project includes open-source implementations of Jakarta EE functionality such as servlets, Java Server Pages (JSP), and more
- when Java web applications are built, a WAR file is generated as the target or build output.
  - JBoss refers to these war files as modules
- most Java IDEs include templates (e.g. Maven archetypes) that create the necessary project structure to build and deploy a web application

## Overview & Concepts {#overview data-title="WildFly Overview"}

- WildFly can be run in `standalone` or `domain` mode
  - There is no difference in features
  - There is only a difference in how servers are managed
- WildFly runs on Windows as a console application and is controllable by batch scripts (e.g. `%JBOSS_HOME%\bin\standalone.bat` or `%JBOSS_HOME%\bin\domain.bat`), or can be hosted as a Windows Service and controlled by the Windows Service Control Manager
- WildFly runs on Linux as a console application and is controllable by shell scripts (e.g. `$JBOSS_HOME/bin/standalone.sh` or `$JBOSS_HOME/bin/domain.sh`), or configured on a per-distro basis to run as a daemon where starting/stopping it depends on the `init` script from the package
- You can customize what modules and configurations are used by the server by modifying the configuration files found in `%JBOSS_HOME%\standalone\configuration` or `%JBOSS_HOME%\domain\configuration`
- You can also pass a configuration file as a parameter to the startup script
  - Standalone: `--server-config`
  - Domain: `--domain-config`
- WildFly includes a Web Based management console out-of-the-box, but it can also be maintained via the command line

## Deploying Java Web Applications to WildFly {#deployment data-title="Deployment"}

- To deploy applications to WildFly, you typically deploy them using the Web Admin Console or the Admin Console CLI
- To deploy to using hte CLI, you would first connect to the WildFly server, then you run the deploy command.

```
%JBOSS_HOME%\bin\jboss-cli.bat --connect 192.168.0.100 --commands="deploy ProfitCalcJava.ear"
```

- You can optionally specify a server list with the --server-groups parameter

## Automation with BuildMaster {#buildmaster data-title="Automation with BuildMaster"}

- the process follows the same pattern as the manual process
- example OtterScript plan:
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
