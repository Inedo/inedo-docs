---
title: Inedo Agent Instancing
subtitle: Using the Same Agent with Multiple Instances
sequence: 20
keywords: inedo, inedo agent

---
As of v44, the Inedo Agent is capable of handling simultaneous connections from multiple instances of BuildMaster and Otter.

To enable this feature, navigate to the Admin->Advanced Settings page in BuildMaster or Otter, and set the `Agent.InstanceId` field to a unique value for each of your instances.

Note that this is only necessary if you have multiple instances of BuildMaster or multiple instances of Otter. BuildMaster and Otter are already isolated from each other even
without setting and instance ID.

After making this change, you should restart the BuildMaster/Otter service.

This feature is available in BuildMaster 6.1.20 or later and Otter 2.2.10 or later.
