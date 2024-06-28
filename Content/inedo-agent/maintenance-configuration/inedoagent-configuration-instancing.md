---
title: "Using the Same Agent with Multiple Instances"
order: 3
---

There are some cases where you may want to have multiple instances of  BuildMaster (or multiple instances of Otter), connect to the same server. For example, you may wish to create a second BuildMaster instance for a migration or simply to segregate applications across business units.

:::(Info) (🙌 Connections from Multiple Instances is Fully Supported)
If you're using Inedo Agent 49+ along with BuildMaster 6.1.20+ or Otter 2.2.10+, this will work automatically. The Inedo Agent will create one instance per incoming connection, based on the product and IP address.
:::

If the BuildMaster or Otter instances share an IP address (i.e. they're behind a router or otherwise NAT'd), you can simply specify the `Agent.InstanceId` property under Admin > Advanced Settings to a unique value for each of your instances.

## How to Connect Legacy/Older Versions
:::(Error)
This only applies to Inedo Agent v44, v45, and v46, and requires 
:::
Agent instancing is possible on versions of the Inedo Agent with BuildMaster 6.1.20+ and  Otter 2.2.10.

### Technical Background
When BuildMaster or Otter connects to an Inedo Agent to perform operations, it transfers certain version-specific files that are used to perform operations on a remote server:

 - Extensions loaded by the connecting instance of BuildMaster/Otter
 - Binary files that are used to start a host process to perform operations

These files must match the versions that exist in the connecting BuildMaster/Otter instance, so BuildMaster/Otter will send updates as needed to make sure everything is consistent.

All of these files that the agent receives are stored in a configurable temporary directory.

### Challenges with Multiple Instances
_For simplicity, this section will only discuss BuildMaster. However, Otter functions in exactly the same way._

When several different versions of BuildMaster connect to the same agent, each of them tries to update the agent's files as described above. This leads to erratic behavior at runtime, since the instance that wins the race and updates the extensions first will work, while the other instance will likely fail if any extensions are different.

### Resolving this with Inedo Agent v44, v45, and v46
Starting with v44, an Inedo Agent is able to handle concurrent connections from multiple instances of BuildMaster by setting one instance ID in BuildMaster. This simply adds a second level of file system isolation for the agent's files, so that each instance ID gets its own isolated set of extensions and host binaries.

To enable this feature, navigate to the Admin > Advanced Settings page in BuildMaster or Otter, and set the `Agent.InstanceId` field to a unique value for each of your instances.

Note that this is only necessary if you have multiple instances of BuildMaster or multiple instances of Otter. BuildMaster and Otter are already isolated from each other, even without setting an instance ID.

After you make this change, you should restart the BuildMaster/Otter service.