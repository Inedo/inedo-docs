---
title: Running as a Different User
subtitle: Executing Tasks as a Different User 
sequence: 500
keywords: buildmaster, deployment, tasks, impersonation, 
show-headings-in-nav: true
---

Every operation within a BuildMaster plan is executed by the user account configured on the BuildMaster service. By default this is the Local System account. At some point you may run into a scenario that prevents you from using the default user and you need to specify a different user to complete an operation or operations. Some common reasons might be:

- Run a script that needs to be executed by a user with higher or different permissions. 
- Access a server that is protected with windows authentication, such as a SQL Server. 

#### How to run a task using Different Credentials

BuildMaster 6.1.11 and later along with InedoAgent v4.3 and later offer this functionality. In order to run sections of your plan as a different user you simply need to add a `with credentials` operation call around the block of script you want to use different credentials for.

In the sample below we are using the [resource credentials](/docs/buildmaster/administration/resource-credentials) named `remote-server-credentials`. You will need to set up a username and password type of resource credential prior to creating your OtterScript. These credentials will be used to gain permission to whatever task you will be running.

```
for server remote-server
{
    with credentials=remote-server-credentials
    {
    // all remote commands executed here will be performed in a process
    // running under the specified credentials
    }
}
```