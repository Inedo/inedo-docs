---
title: "What is a \"server role\" in Otter?"
order: 3
---

In Otter, a [server](/docs/otter/connecting-to-your-servers-with-otter/otter-servers-in-otter) can be assigned any number of "server roles". 

A server role contains [desired configuration](/docs/otter/collecting-verifying-configuration/otter-desired-configuration-with-otterscript) that describes what you want the server to do, what needs to be installed, or any other configuration. For example, you could create a server role (`iss-server`) that describes exactly how you want IIS to be configured on web servers, and a simpler role (like `rdp-enabled`) that would describe how you want RDP settings configured. You can assign both of those roles to a single server, so that you can have web servers you can RDP into.


:::(Info) (Best Practices: Granularity of Server Roles)
 While Otter can handle both highly granular server roles (i.e., thousands) and very simple server roles (i.e., one that describes the entire configuration for a specific server), we recommend against both. The tool may have no problem with extreme granularity, but your human teammates will. And making one server role define everything is equally inadvisable; it'll be hard for people to read.
:::


## Creating Server Roles

You create a server role by navigating to Roles > Create Roles, or use the [Infrastructure API](/docs/otter/reference/api/infrastructure) to programmatically create one.

![Creating a Server Role](/resources/docs/otter-server-role-new.png)

After creating a server role, you can navigate to the "Desired Configuration" tab to create OtterScript that will describe your server's configuration.

![Tabs on the Server Role Page](/resources/docs/otter-server-role-tabs.png)

See [Desired Configuration with OtterScript ](/docs/otter/collecting-verifying-configuration/otter-desired-configuration-with-otterscript) to learn more.


## Assigning Servers to Roles
You can assign roles from the server page ("assign role") or from the server role page ("assign servers").

![](/resources/docs/Assigning-Servers-to-Roles.png){height="" width=""}



## Server Role Variables

You can add [Configuration Variables](/docs/otter/scripting-in-otter/otter-configuration-variables) to server roles as well. These will be used by your desired configuration OtterScript, and can help you create more reusable OttreScript inside.

You can add server role variables by navigating to Roles > Server Role> “add” in “Variables” column. 

:::(Info) ()
Admins can “obscure the value of this variable from casual viewing” to add a layer of protection; this is NOT intended as a security measure, however.  
:::

![](/resources/docs/Server-Role-Variables.png){height="" width=""}

## Server Roles and Environments
Servers are assigned to roles (e.g., “accounting”) and environments (e.g., “Test”). An environment cannot be assigned a role. See [What is an "environment" in Otter?](/docs/otter/connecting-to-your-servers-with-otter/otter-modeling-infrastructure-environments) to learn more.

## Dependencies and Dependent Roles

Role dependencies allow you to model complex server and application configuration through hierarchical sets of simple roles with dependencies. This makes it much easier to share common configuration and define smaller-but-related roles.

You can define a role's dependencies (i.e., the other roles which are required) on the role overview page.

### Example: Accounts

To configure a server to have AccountsWeb, a web-based front-end for the Accounts application, that server must have IIS installed and configured. In Otter, you could represent this using two separate roles (accounts-web role and iis-server) and, using role dependencies, configure the accounts-web role to depend upon the iis-server role. This way, when you assign the accounts-web role to a server, the iis-server role will also be used to configure the server without needing to explicitly assign it to the server.

Expanding on the Accounts example further, we can define each application component as a separate role (accounts-db, accounts-svc, accounts-api, and accounts-web), and further separate out the non-accounts specific configuration into their own roles.

![Role Dependency Diagram](/resources/docs/role-dependencies-ex.png)

We could create a "placeholder" role (accounts-full) that simply depends on the four component roles, and would allow the entire account stack to be installed on a single server. Or, different servers could be assigned different Account roles.

## Configuration Runtime Behavior

When a configuration job is run against a server, Otter will assemble the server’s and its roles’ configuration plans into a single plan, and then execute it. Note that:

- Dependencies run first; when assembling the plan, each role’s dependencies (and each dependency’s dependencies) are run first to ensure required configuration happens before the rest.
- Circular dependencies result in runtime errors

Otherwise, the configuration jobs will run as if all roles were added to the server directly.

## Resource Pools

A resource pool is a set of servers (defined by a role) that are used as a single, load-balanced resource. You may acquire an unused server from a resource pool, and then release it back into the pool once the needed tasks have been performed.

Visit the [Resource Pools documentation of the Inedo Execution Engine](/docs/executionengine/overview/resource-pools) for more information on how to configure a resource pool.