---
title: Runtime Variables
keywords: otter,otterscript,variables
---

Variables allow for extreme flexibility when modeling your infrastructure, and creating [Plans](/support/documentation/otter/core-concepts/plans). There are a lot of uses for variables, some of the most common are:

{.docs}
- Arguments/properties of [Operations](overview#operations)
- Conditional execution using a [If/Else Block](statements-and-blocks/if-else)
- Iteration/enumeration using a [Loop Block](statements-and-blocks/loop)


Like [Configuration Variables](../global-components/configuration-variables), which are essentially values that you can assign to a [Server](/support/documentation/otter/core-concepts/servers), [Server Role](/support/documentation/otter/modeling-infrastructure/server-roles), [Environment](/support/documentation/otter/modeling-infrastructure/environments), etc., runtime variables are used by the [Execution Engine](/support/documentation/otter/execution-engine/overview) while running a plan.

## Runtime Variable Types {#types}

There are three types of runtime variables, and each is denoted by a prefix.

Prefix | Type   | Description
-------|--------|----------
$      | String | a single value
@      | List   | an enumerable set of either Lists or Strings
%      | Map    | a set of key/value pairs with values being a String, List, or Map

String variables are the most common, and are used just about anywhere. Whenever you reference a string variable within another block or statement, it is automatically replaced with the value. You can escape this with the grave apostrophe (`` ` ``) character.

<tab-block>
    <tab name="Visual Editor">
        <img class="screenshot" src="/resources/documentation/otter/variable-pic.png" alt="Setting a Variable in Otter" />
    </tab>
    <tab name="Text Editor (OtterScript)">
        <img class="screenshot" src="/resources/documentation/otter/variable-text.png" alt="Setting a Variable in Otter" />
    </tab>
    <tab name="Execution Log">
        <img class="screenshot" src="/resources/documentation/otter/variable-log.png" alt="Setting a Variable in Otter" />
    </tab>
</tab-block>

List variables are used much less frequently, primarily as the source of a [Loop Block](statements-and-blocks/loop).

Map variables are pretty rare, and are only used in operations needing an arbitrary list of key/value pairs (like the `HTTP::Post`) or in other really advanced scenarios.

## Runtime Variable Scoping {#scoping}

When you create a runtime variable with the [Set Variable Value](/support/documentation/otter/execution-engine/statements-and-blocks/other-statements#set-variable) statement, that variable will be accessible in the current and nested blocks. For example:

<tab-block>
    <tab name="Visual Mode">
        <img class="screenshot" src="/resources/documentation/otter/variable-nested-pic.png" alt="Setting a Nested Variable in Otter" />
    </tab>
    <tab name="Text Mode (OtterScript)">
        <img class="screenshot" src="/resources/documentation/otter/variable-nested-text.png" alt="Setting a Nested Variable in Otter" />
    </tab>
    <tab name="Execution Log">
        <img class="screenshot" src="/resources/documentation/otter/variable-nested-log.png" alt="Setting a Nested Variable in Otter" />
    </tab>
</tab-block>

While setting both runtime and [Configuration Variables](/support/documentation/otter/global-components/configuration-variables) may lead to confusion if over used, Otter does allow for greater re-use of plans and templates by allowing the same plan or template to be used at different development environments where things like certificates, keys, and data might *need* to be different for testing.

## Relation to Configuration Variables {#relation}

When a string variable is referenced in your plan, the execution engine first checks to see if there is a runtime variable with that name in the current scope. If not, then a configuration variable with that name is retrieved.  We generally don't recommend doing this, as it may get quite confusing if others are trying to understand your plan.

## Variable Functions {#variable-functions}

You may have seen expressions like `$PathCombine($RootPath,Accounts)` used in previous examples; these are variable functions, which take in a number of parameters and return a value.

All of the "built-in variables" like `$WorkingDirectory`, `$EnvironmentName`, and `@AllServers` are implemented as variable functions.

You can create configuration or runtime variables with the same name of a variable function, but we don't recommend it because it can get quite confusing if others are trying to understand your plan. If there is a variable of the same name, that value will be used instead, unless you explicitly reference the parameter list (such as `$EnvironmentName()`).

Variable functions are extensible, so you can write your own with an [Otter Extension](../administration/extensions).
