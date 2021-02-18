---
title: Manual Installation
sequence: 40
show-headings-in-nav: true
---

**Note:** agents should be installed on remote servers, and are not required to be installed on the Otter or BuildMaster server itself. {.info}

**Note:** the agent version you download MUST be the same version as the Otter or BuildMaster software. Do not attempt to install a newer agent version, or the software will report errors when attempting to connect to the agent. Older agents (provided the major version number is the same) will automatically be upgraded to the same version as the BuildMaster software. {.info}

1. Download the [Otter manual installation package](/otter/versions).
2. Extract the **Otter-Agent.zip** file from the archive, then extract the files from **Otter-Agent.zip** to the directory where you want to install the agent (for example, C:\Otter\Agent).
3. Edit the **InedoAgentService.exe.config** file and change the value of the **Otter.RootPath** configuration item to a directory where the Otter Agent will be allowed to write files (example, C:\Otter\AgentData).
4. Open a command prompt window as Administrator in the directory where the Otter agent was extracted and type **InedoAgentServer.exe install**
5. Start the service by typing **sc \\\localhost start INEDOAGENTSVC**
6. That's it! You'll also want to make sure **port 46336** is open in your firewall. Currently the port is not configurable, but it will be in a future version.
7. By default, the agent is installed to run as **Local System**, giving it full access to the machine. If you do not want to give it full control, you may change the service account to something more suitable in the services.msc dialog.
