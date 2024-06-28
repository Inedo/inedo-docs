---
title: "Troubleshooting"
order: 4
---

Here are the most common Inedo Agent errors as we have noted from our various support channels.

### How to remove one or more stuck agent instances

The best way to remove instances is to stop the Agent service and delete all the temporary files.

To do this:
1. Stop the `INEDOAGENTSVC` Windows service. 
2. Navigate to the temporary directory.  You can find this by opening `C:\Program Files\InedoAgent\InedoAgentService.exe.config` and locating the key named `Otter.RootPath`.  This value contains the value of the temporary directory.
3. Delete all folders and files from the temporary directory. 
4. Start the `INEDOAGENTSVC` Windows service.