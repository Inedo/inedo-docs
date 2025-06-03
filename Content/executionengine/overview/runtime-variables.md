---
title: "Runtime Variables"
order: 4
---

Variables allow for extreme flexibility when modeling your [deployment plans](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts). There are many uses for variables, some of the most common are:

*   Arguments/properties of [Operations](/docs/executionengine/overview/operations-functions)
*   Conditional execution using an [If/Else Block](/docs/executionengine/otterscript/statements-and-blocks/if-else)
*   Iteration/enumeration using a [ForEach Block](/docs/executionengine/otterscript/statements-and-blocks/foreach)

Like [configuration variables](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables)—which are essentially values that you can assign to a server—application, environment, release, etc., runtime variables are used by the execution engine while running a plan.

Runtime Variable Types
----------------------

There are three types of runtime variables, and each is denoted by a prefix.

| Prefix | Type | Description |
| --- | --- | --- |
| $ | String | A single value |
| @ | List | A enumerable set of either Lists or Strings |
| % | Map | A set of key/value pairs with values being a String, List, or a Map |

String variables are the most common, and are used just about anywhere. Whenever you reference a string variable within another block or statement, it is automatically replaced with the value. You can escape this with the grave apostrophe (`` ` ``) character.

### Visual Editor
![](/resources/docs/otterscript-variable-editor.png){height="" width=""}

### Text Editor (OtterScript)
![](/resources/docs/otterscript-variable-text.png){height="" width=""}

### Execution Log
![](/resources/docs/otterscript-variable-log.png){height="" width=""}

`foreach` variables are used much less frequently, primarily as the source of a [ForEach Block](/docs/executionengine/otterscript/statements-and-blocks/foreach).

Map variables are pretty rare, and are only used in operations needing an arbitrary list of key/value pairs (like the `HTTP::Post`) or in other really advanced scenarios.

### Runtime Variable Scoping

When you create a runtime variable with the [**Set Variable Value**](/docs/executionengine/otterscript/statements-and-blocks/other) statement, that variable will be accessible in the current and nested blocks. For example:

### Visual Mode
![](/resources/docs/otterscript-variable-nested.png){height="" width=""}

### Text Mode (OtterScript)
![](/resources/docs/otterscript-variable-nested-text.png){height="" width=""}

### Execution Log
![](/resources/docs/otterscript-variable-nested-log.png){height="" width=""}

While setting both runtime and [Configuration Variables](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables) may lead to confusion if overused, it does allow for greater reuse of [plans](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts) and templates by allowing the same plan or template to used at different development environments where things like certificates, keys, and data might _need_ to be different for testing.

## Relation to Configuration Variables

When a string variable is referenced in your plan, the execution engine first checks to see if a runtime variable with that name exists in the current scope. If not, a configuration variable with that name is retrieved. We generally do not recommend doing this, as it can get quite confusing when others try to understand your plan.

## Variable Functions { #variable-functions }

You may have seen expressions like `$PathCombine($RootPath,Accounts)` [used in examples](https://inedo.com/support/tutorials); these are variable functions, which take in a number of parameters and return a value. All of the "built-in variables" like `$WorkingDirectory`, `$EnvironmentName`, and `@AllServers` are implemented as variable functions.

You can create configuration or runtime variables with the same name of a variable function, but we do not recommend this because it can get quite confusing when others try to understand your plan. If there is a variable with the same name, that value will be used instead unless you explicitly reference the parameter list (such as `$EnvironmentName()`).

Variable functions are extensible, so you can write your own with a [BuildMaster Extension](/docs/buildmaster/reference/extensions).