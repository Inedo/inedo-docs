---
title: "OtterScript & Operations"
order: 3
---

:::(internal) (TODO)
- Provide a sort of cheatcheat of basic statements
:::

OtterScript is a Domain-Specific Language (DSL) used by Otter and BuildMaster to automate deployments, orchestration, and configuration. Although it's based on programming logic, you don't need to be a programmer to use OtterScript, which provides both a text editor and a low-code visual editor.

![buildmaster-osve](/resources/docs/buildmaster-osve.gif){height="" width="50%"}

## Writing Scripts with OtterScript
No programming skills are required to work with OtterScript; you use the low-code visual editor to drag-and-drop the statements you need to build or deploy your applications. But if you're familiar with programming or scripting, you'll already know how much of OtterScript works.

:::(internal) (TODO)
Provide a sort of cheatcheat of basic statements
* operations
* if/else
* try/catch
* for loops
* common statements (log, set variable)
* link to advanced scenarios 
:::

To learn more, see the [OtterScript Overview](/docs/executionengine/otterscript/overview) documentation. There is a [Formal Specification](/docs/executionengine/reference/formal-specification) available if you want to know precisely how OtterScript works.




## What are Operations in OtterScript?
Just about everything in OtterScript is accomplished through Operations. Whether it's the `Deploy-Artifact` operation that is used to deploy applications to servers, or the `IIS::Ensure-AppPool` and `IIS:Ensure-Site` operations used to configure a website on a server. 

BuildMaster includes many [built-in operations](/docs/buildmaster/reference/operations), and you can even write your own using the [Inedo SDK](/docs/inedosdk).

## How do "Ensure" Operations Work?

When you browse operations in BuildMaster, you'll notice that many will start with `Ensure-`. While these operations play a key role in [Otter's drift detection and configuration remediation](/docs/otter/overview), in BuildMaster, they effectively just mean "create or update".

For example, `IIS::Ensure-AppPool` will create an Application Pool in IIS if it doesn't already exist, or update the configuration of that application pool.

### Create- vs. Ensure- Operations
In some cases, there will be a similar `Create-` and `Ensure-` operation; these operations behave nearly identically in BuildMaster. For example, there is both a `Create-File` and an `Ensure-File` operation.  

::: (info) (BEST PRACTICE: Prefer Create- Operations in BuildMaster)
When both types of operations are available, you should generally choose a `Create-` operation because the behavior will be more intuitive for deployment purposes.
:::

The [Create-File Operation](/docs/buildmaster/reference/operations/general/create-file) creates the file you specify. If the file already exists, it will delete the old file and create a new one. Or, it will throw an error if  `Overwrite` is not set to `true`. 

```OtterScript
Create-File hello-world
(
  Text: hello world!,
  Overwrite: true
)
```
The [Ensure-File Operation](/docs/buildmaster/reference/operations/general/ensure-file) will also create the file that you specify. But if the file exists, it will update only the properties you specify.

```OtterScript
Ensure-File hello-world.txt 
(
  Text: hello world!
);
```
In this case, the file's modified date will not be changed, because `Modified` is one of the operation's parameters. That behavior may be unexpected, because the file was modified.