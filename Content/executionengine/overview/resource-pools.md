---
title: "Resource Pools"
order: 3
---

Through the use of server roles, it's fairly easy to ensure that any number of servers have an identical set of applications and configuration. This is quite useful for building applications at scale: you can simply define an "app-node" role to represent a web frontend, and then target all servers in that role to configure and deploy changes to.

While this role targeting is typically done at the job (Otter) or pipeline (BuildMaster/Hedgehog) level, it can just as easily be done in OtterScript:

```
foreach server in @ServersInRole(app-node)
{
    IIS::Stop-AppPool AccountsAppPool;

    ProGet::Get-Package
    (
        Credentials: ProGetLocal,
        Feed: Initech-Apps,
        Name: Accounts,
        Version: $ReleaseNumber,
        Directory: c:\Websites\Accounts
    );

    IIS::Start-AppPool AccountsAppPool;
}
```
<br>

Upon completion of that block, all servers with the app-node role will have had their AccountsAppPool stopped and started, as well as received a version of the Accounts package.

There are times when you want to perform something on only a single server in a role. For example, if you have a long-running task like automated UI testing, you'd only want one of your available web-ui-testing servers to perform that task. This is where Resource Pools come in.
<br>

## Defining a Resource Pool
A resource pool is a set of servers that are used as a single, load-balanced resource. You may acquire an unused server from a resource pool, and then release it back into the pool once the needed tasks have been performed.

Roles are used to define resource pools. So, to create a resource pool for a task, simply define a role, and place the desired servers in it.
<br>

## Acquiring a Server from a Resource Pool
The `Acquire-Server` operation is used to request a server from a resource pool.

```
Acquire-Server
(
    Role: net45-buildserver,
    AcquiredServer => $AcquiredServer
);
```
When run, the operation will find a server that has not yet been "acquired" for the specified scope. In the above example, let's say the net45-buildserver Resource Pool has three servers: 1, 3, and 5. If server 1 has already been "acquired" by another orchestration plan, the operation will then check if server 3 is available and will "acquire" it for our task.

The scope defaults to global, but can be set to role on the advanced tab. A role-scoped acquisition means that the same server can be acquired for different roles at the same time, whereas global scopes require exclusivity.

The following occurs when a server is found: the server is marked as "acquired" for the scope, the `$AcquiredServer` output variable is assigned, and the operation quits, continuing the execution.

If all servers in the role have already been acquired, the acquisition request is enqueued, and will be attempted when a server is released. The execution will pause until that happens.
<br>

## Releasing a Server back into a Resource Pool
Once the acquired server has finished performing the needed tasks, the server may be released back into the pool via the `Release-Server` operation.

```
Release-Server
(
    Role: net45-buildserver,
    Server: $AcquiredServer
);
```
<br>

When run, the operation will iterate over all acquired servers to find one that matches the specified role and server name. If no such server is found, a warning will be written to the logs. Otherwise, the specified server is removed from the acquired list, and all activation requests are run.

Additionally, when an execution completes, all servers that have been acquired during the execution are automatically released back to the Resource Pool.
<br>

## Identifying Acquired Servers
The `@AcquiredServers` variable function is used to list all acquired servers for a specified role.

```
foreach $Server in @AcquiredServers(net45-buildserver)
{
    Log-Information $Server is currently being used.;
}
```
You should generally only use this for informational purposes.
<br>

## Usage Notes
These operations do not marshal actual server usage, which means you have the freedom to bypass a resource pool you create. Aside from common sense, this is to prevent you from simply doing a `foreach server in @ServersInRole(net45-buildserver)` loop to perform tasks on servers that have already been acquired. Obviously, you should not do this.

You should, however, release servers after use. While they are freed at the end of each execution, it's possible an unrelated operation will cause an execution to run longer than needed.

You should generally avoid role-scoped acquisitions unless you need to use them for a very specific purpose.