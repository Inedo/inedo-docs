---
title: "System Replication Overview"
order: 2
---

ProGet's system replication feature allows multiple ProGet instances to synchronize vulnerability assessments, vulnerability assessment types, and policies *(coming soon...)* from another instance.


## Configurations
A "replication" requires at least two instances of [ProGet Enterprise Edition](https://inedo.com/proget/enterprise). AT least one instance must be configured for incoming communication, typically configured to push content to the other instances, and the other instance(s) must be configured for outgoing communication, typically configured to pull content from the other instance.

### Communication Mode
At least one instance is configured for "outgoing" communication (similar to how a web browser accesses a web server), the other for "incoming" communication (like a web server responds to a web browser).

::: (INFO)
System replication is available in ProGet 2025.13 and later.
:::

Behind the scenes, incoming communication is handled by the System Replication API endpoint hosted on the ProGet Web Application, just like all other API endpoints. This means that access via HTTP/S occurs in the same way that your browser and other client tools interact with ProGet.

Outgoing communication is handled by the ProGet Service application, which communicates with the other instance's Replication API endpoint. The "outgoing" instance is where most of the work is done, and where you'll find detailed replication logs.


### Replication Mode
Regardless of the communication mode you choose, you can configure how content (vulnerability assessments, assessment types, and policies) is synchronized between ProGet instances.

Each instance must be configured in one of the following ways:
* **Pull Content from other Instance**; Download missing content from the other instance and delete content that was deleted from this instance. The other instance should be configured to Push Content.
* **Push Content to other Instance**; Upload missing content to the other instance and delete content that was deleted on this instance. The other instance should be configured to Pull Content.

### Replication Options
When configuring system replication, you can choose to replicate any combination of the following content:

* **Vulnerability Assessments & Assessment Types**; Vulnerability assessments and assessment types will be replicated from the push instance to the pull instance.
* **Policies**; *(coming soon...)* Policies will be replicated from the push instance to the pull instance.

:::(WARN) (IMPORTANT:)
When an instance is configured to pull content, any existing content on that instance will be deleted and replaced with the content from the push instance.
:::

## Best-Practices
When configuring system replication, we recommend using a hub and spoke approach.  

* The hub should be configured for incoming communication and that will push content to the other instances. 
* Each spoke should be configured for outgoing communication and that will pull content from the hub.  

This allows all vulnerability assessments, assessment types, and policies to be maintained on the hub and pushed to the spokes.
