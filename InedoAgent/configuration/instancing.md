---
title: Inedo Agent Instancing
subtitle: Using the Same Agent with Multiple Instances
sequence: 100
keywords: inedo, inedo agent

---


## Technical Background

When BuildMaster or Otter connects to an Inedo Agent to perform operations, it transmits certain version-specific files that are used to execute operations on a remote server:

 - Extensions loaded by the connecting instance of BuildMaster/Otter
 - Binaries used to start a host process to execute operations

These files must match the versions present in the connecting BuildMaster/Otter instance, so BuildMaster/Otter will send updates as needed to ensure everything is consistent.

All such files received by the agent are stored in a configurable temporary directory.

## Connecting Multiple Versions of a Product

__Note: As of Inedo Agent v49, instancing happens automatically based on the connecting product's IP address.__

_For simplicity, this section will only discuss BuildMaster. However, Otter functions in exactly the same way._

If multiple different versions of BuildMaster connect to the same agent, each will try to update the agent files as described above. This results in erratic behavior at runtime, because whichever instance wins the race and updates extensions first will work, while the other instance will likely fail if any extensions are different.

As of v44, one Inedo Agent is capable of handling simultaneous connections from multiple instances of BuildMaster by setting an instance ID in BuildMaster. Effectively, all this does is add a second level of file-system isolation for the agent's files, so that each instance ID gets its own isolated set of extensions and host binaries.

To enable this feature, navigate to the Admin->Advanced Settings page in BuildMaster or Otter, and set the `Agent.InstanceId` field to a unique value for each of your instances.

Note that this is only necessary if you have multiple instances of BuildMaster or multiple instances of Otter. BuildMaster and Otter are already isolated from each other even
without setting an instance ID.

After making this change, you should restart the BuildMaster/Otter service.

Agent instancing requires BuildMaster 6.1.20 or later or Otter 2.2.10 or later.

_A future agent version will make this extra configuration step unnecessary, and will automatically isolate instances by source IP address. This page will be updated when this improvement is available._
