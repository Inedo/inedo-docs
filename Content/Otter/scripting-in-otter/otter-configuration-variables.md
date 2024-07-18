---
title: "What are \"variables\" in Otter?"
order: 2
---

Variables can help you create reusable scripts that can run in different environments, as well as to prompt users for input parameters before they run your script.
Essentially, variable can be used in place of any value that you might want to change when your script is run, such as: 
* Directory where an application is located 
* Version of a package that should be installed 
* User account that service runs under 
* Name of a certificate to install 
* Port that a website should use 
* Email address to send a notification to 

## What is a "variable" in Otter?
Variables can refer to several different things inside of Otter.

| Type | Description |
| --- | --- |
| **Configuration Variables** | are static values  that you can set on servers, roles, environments, or globally. You can add these from the UI, through the API, or using an operation in OtterScript. |
| **Variable prompts** | defined in [Job Templates](/docs/otter/orchestration-server-automation/otter-jobs-templates) to prompt a user for input when they run a job; these are displayed as input fields and are validated. When the user runs the job, they are added as Runtime Variables |
| **Variable functions** | predefined functions like `$Date()` or `$ServerName()` that will automatically change depending on when and where you scripts are run; although the parenthesis are optional,  some variable functions take parameters, like `$FileExists(fileName)`<br /><br/> You can create your own variable functions with the [Inedo SDK ](/docs/inedosdk/versions-release-notes) |
| **Variable expression** | these are strings that "look" like variables (e.g. `$MyVariable`), and that instruct Otter to process the string as if it were a variable; if you use a variable expression in OtterScript that doesn't reference any known variable (e.g. `$NotRealVariable`), you'll get a runtime error in OtterScript. |
| **Runtime variables** | these exist [within your OtterScript script](/docs/otter/scripting-in-otter/otter-configuration-variables), and behave like variables in programming languages; they are intended to help you create advanced programming logic in your scripts |


## Working with Configuration Variables 
**Configuration variables** are static values that you can set on servers, roles, environments, or globally. You can add these from the UI, through the API, or using an operation in OtterScript. 
 
### Editing Variables in Otter's UI 
You can define variables by going to the desired context (such as the specific server or role) and then adding a single one. You can also bulk edit all variables in that context.

![](/resources/docs/Editing-Variables-in-Otter's-UI-01.png){height="" width=""}

You can see all configuration variables across all contexts by navigating to Admin > Configuration Variables.  This also lets you bulk edit all variables in Otter.

![](/resources/docs/Editing-Variables-in-Otter's-UI-02.png){height="" width=""}
 
### Hiding & Obscuring Values 
Configuration variables include the option to “obscure the value of this variable from casual viewing.”  As the name implies, this only obscures the value by replacing it with "(hidden)" in the user interface. It is not intended for passwords, nor should this be used as a security feature. 

:::(Warning)
Do not store  sensitive information in variables; use [secure credentials](/docs/otter/configuring-for-your-team/otter-global-components-resource-credentials) instead.
:::
 
### Variables inside of other Variables 
When creating a configuration variable, there is an option to "Evaluate the value of this variable will be as if you ran $Eval() over it". 

This option instructs Otter to evaluate variable expressions inside of your variable, which means that you can reference one variable from within another. This can help simplify the configuration variables you maintain in Otter across a lot of environments, servers, server roles, etc.

For example, let's say that you had dozens of servers that need to have the `FooBar` and `MyBar` applications installed, and you wanted them installed in a mostly consistent manner. You could set up configuration variables as follows:

| Scope | Name | Value |
| --- | --- | --- |
| Global | `$AppsDrive` | `D:\` |
| Global | `$WebSiteRoot` | `$PathCombine($AppsDrive, WebSites)` |
| Environment<br/>(Production) | `$AppsDrive` | `F:\` |
| Server Role<br/> (FooBar) | `$FooBarAppPath` | `$PathCombine($WebSiteRoot, $EnvironmentName, FooBar)` |
| Server Role<br/> (MyBar) | `$MyBarPath` | `$PathCombine($WebSiteRoot, $EnvironmentName, MyBarApp)` |

With this scheme, on a production server with the `MyBar` role, the `$MyBarPath` variable would resolve to `F:\Production\MyBarApp`. This simplifies having to define the configuration variable over and over again.

## Using the API to Read or Set Variables 
You can use the [Variables Management API](/docs/otter/reference/api/variables) to programmatically read and set variables in Otter. This allows scripts and programs external to Otter to see the values you've defined, as well as set them on environments, servers, server roles, etc.

```PowerShell
# Get value of FooBarVersion on FooBar role
$foobarVersion = Invoke-WebRequest -URI https://my-otter.corp.local/api/variables/role/foobar/FooBarVersion?key=$myApiKey

# Set value of FooBarVersion if doesn't exist
if (!$foobarVersion) {
  Invoke-WebRequest -URI https://my-otter.corp.local/api/variables/role/foobar/FooBarVersion?key=$myApiKey -Method 'POST' -Body '3.2.4'
}
```
Note that `$myApiKey` refers to an [API Key](/docs/otter/configuring-for-your-team/otter-administration-security-api-keys) that you'll need to set before use.

## Using OtterScript to Set Variables 
You may set any variable type using OtterScript. This is best for when you want to set variable as part of an orchestration in Otter (e.g., when provisioning a new server using OtterScript). 

To do this, first create an [API Key](/docs/otter/configuring-for-your-team/otter-administration-security-api-keys) with Variables Management permission. Then, create an Inedo Otter [secure resource](/docs/otter/configuring-for-your-team/otter-global-components-resource-credentials) with the same URL as your Otter server, and that uses an API Key/Token [secure credential](/docs/otter/configuring-for-your-team/otter-global-components-resource-credentials). 

This allows you to use the `Otter::Set-Variable` operation. 

## Multiple and Cascading Variable Values 
The only place configuration variables can be used are in scripts. Configuration variables cascade. This means that you can define a variable of the same name in multiple places, and the correct value will be resolved at runtime.  

For example, if you define a variable named `$HDarsPath` on the Testing environment, when an orchestration or configuration plan runs against a server in the Testing environment, that variable will resolve at runtime. If you also defined `$HDarsPath` on a single server in the Testing environment, then that value would be used instead. This allows for reusing scripts without having to change local variables. 

### Resolution Rules
The variable definition that's the "closest" match to the current context is used. This is determined as follows: 

1.   **Script**
2.   **Server**
3.   **Environment**
4.   **Server Role**
5.   **Global**

For example the variable `$UserName` could be defined as "Admin" at the global-level but as "Administrator" at the server-level for IntSv03. When calling the variable in a job  on IntSv3, the variable will be defined as "Administrator" instead  of "admin".

### Multiple Scopes
 You can also assign multiple scopes to a configuration variable; for example, you could define a variable that's associated with both the Testing environment and the foobar-web role. A multi-scope variable simply adds precedence to the highest-scope (Server is still "closer" than a server role).

:::(Error) 
Assigning multiple scopes to a single configuration variable can become confusing as the resolution rules are not intuitive. We generally discourage this use.

You can only create multi-scoped variables from the administration section, and they are visible (but not editable) on the servers, server roles, etc., they are associated with. 
:::
