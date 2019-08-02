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

You can run sections of your plan as a different user very easily with the `with credentials` operation .

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