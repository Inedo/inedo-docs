---
title: "Runtime Variables"
order: 8
---

Variables allow for extreme flexibility when modeling your infrastructure and creating [Plans](/docs/otter/scripting-in-otter/otter-otterscript-and-operations). There are many uses for variables, some of the most common are:

- Arguments/properties of [Operations](/docs/executionengine/overview/executionengine-components-operations-functions)
- Conditional execution using an [If/Else Block](/docs/executionengine/overview/statements-and-blocks/otter-execution-engine-statements-and-blocks-if-else)
- Iteration/enumeration using a [Loop Block](/docs/executionengine/overview/statements-and-blocks/otter-execution-engine-statements-and-blocks-loop)

Like [Configuration Variables](/docs/otter/scripting-in-otter/otter-configuration-variables), which are essentially values that you can assign to a [Server](/docs/otter/connecting-to-your-servers-with-otter/otter-servers-in-otter), [Server Role](/docs/otter/connecting-to-your-servers-with-otter/otter-modeling-infrastructure-server-roles), [Environment](/docs/otter/connecting-to-your-servers-with-otter/otter-modeling-infrastructure-environments), etc., runtime variables are used by the [Execution Engine](/docs/otter/scripting-in-otter/otter-otterscript-and-operations) while running a plan.

## Runtime Variable Types
There are three types of runtime variables, and each is denoted by a prefix:

Prefix | Type   | Description
-------|--------|----------
$      | String | a single value
@      | List   | an enumerable set of either Lists or Strings
%      | Map    | a set of key/value pairs with values being a String, List, or Map

String variables are the most common, and are used just about anywhere. Whenever you reference a string variable within another block or statement, it is automatically replaced with the value. You can escape this with the grave apostrophe (`` ` ``) character.

**Visual Editor**
![variable-pic.png](/resources/docs/variable-pic%282%29.png){height="" width=""}

**Text Editor (OtterScript)**
![variable-text.png](/resources/docs/variable-text%281%29.png){height="" width=""}

 **Execution Log**
![variable-log.png](/resources/docs/variable-log%281%29.png){height="" width=""}

List variables are used much less frequently, primarily as the source of a [Loop Block](/docs/executionengine/overview/statements-and-blocks/otter-execution-engine-statements-and-blocks-loop).

Map variables are pretty rare, and are only used in operations needing an arbitrary list of key/value pairs (like the `HTTP::Post`) or in other really advanced scenarios.

## Runtime Variable Scoping

When you create a runtime variable with the [Set Variable Value](/docs/executionengine/overview/statements-and-blocks/otter-execution-engine-statements-and-blocks-other-statements#set-variable) statement, that variable will be accessible in the current and nested blocks. For example:

**Visual Mode**
![variable-nested-pic.png](/resources/docs/variable-nested-pic.png){height="" width=""}

**Text Mode (OtterScript)**
![variable-nested-text.png](/resources/docs/variable-nested-text%281%29.png){height="" width=""}

**Execution Log**
![variable-nested-log.png](/resources/docs/variable-nested-log%281%29.png){height="" width=""}

While setting both runtime and [Configuration Variables](/docs/otter/scripting-in-otter/otter-configuration-variables) may lead to confusion if overused, Otter does allow for greater reuse of plans and templates by allowing the same plan or template to be used at different development environments where things like certificates, keys, and data might *need* to be different for testing.

## Relation to Configuration Variables

When a string variable is referenced in your plan, the execution engine first checks to see if a runtime variable with that name exists in the current scope. If not, a configuration variable with that name is retrieved. We generally do not recommend doing this, as it can get quite confusing when others try to understand your plan.

## Variable Functions 

You may have seen expressions like `$PathCombine($RootPath,Accounts)` used in previous examples; these are variable functions, which take in a number of parameters and return a value.

All of the "built-in variables" like `$WorkingDirectory`, `$EnvironmentName`, and `@AllServers` are implemented as variable functions.

You can create configuration or runtime variables with the same name of a variable function, but we do not recommend this because it can get quite confusing when others try to understand your plan. If there is a variable with the same name, that value will be used instead unless you explicitly reference the parameter list (such as `$EnvironmentName()`).

Variable functions are extensible, so you can write your own with an [Otter Extension](/docs/otter/administration-maintenance/otter-administration-extensions).