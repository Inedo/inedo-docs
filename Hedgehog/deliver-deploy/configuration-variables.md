---
title: Configuration Variables
sequence: 300
keywords: hedgehog, variables, executions
show-headings-in-nav: true

---
[Variables](/support/documentation/executionengine/components/runtime-variables) allow for extreme flexibility when modeling your deployment plans, and configuration variables allow you to have the same plan run differently across different servers.

You can define configuration variables at different scopes (release, server, environment, global, etc), and then reference those variables in your plan when using Operations, [If/Else Blocks](/support/documentation/executionengine/otterscript/statements-and-blocks/if-else), [Loop Blocks](/support/documentation/executionengine/otterscript/statements-and-blocks/loop), etc. You can also create a [runtime variables](/support/documentation/executionengine/components/runtime-variables) in a plan itself.

## Cascading Variables {#cascading data-title="Cascading Variables"}

Configuration Variables cascade, which means that you can define a variable of the same name in multiple places, and the correct value will be resolved at runtime. For example, if you define a variable named `$HDarsPath` on the `Testing` environment, when a plan runs against a server in the `Testing` environment, that variable will resolve at runtime.

If you also defined `$HDarsPath` on a single server in the `Testing` environment, then `that` value would be used instead.

This allows for reusing Plans and Templates without having to change local variables. There are times when it may be appropriate for variables to have different values when they are used different places, and Hedgehog allows for this.

### Resolution Rules {#resolution-rules data-title="Resolution Rules"}

The variable definition that's the "closest" match to the current context is used. This is determined as follows:

{.docs}
- Deployment
- Package
- Releases
- Project
- Server
- Server Role
- Environment
- Global

### Multiple Scopes {#multiple data-title="Multiple Scopes"}

You can also assign multiple scopes to a configuration variable; for example, you could define a variable that's associated with *both* the `Testing` environment and the `hdars-web` role. A multi-scope variable simply adds precedence to the highest-scope (Package is still "closer" than a Environment + Project).

However, this get can get a bit tricky as the resolution rules are simply not intuitive, so we generally discourage this use. You can create multi-scoped variables from only the Administration section, and they are visible (but not editable) on servers, server roles, etc., they are associated with.

## Variable Types {#variable data-title="Variable Types"}

Configuration variable values may be set to any of the 3 runtime types. Configuration variable values with the following syntax will resolve to the associated type:

{.docs}
- **list** - expression follows the format: `@(item1, item2, ...)`
- **map** - expression follows the format: `%(key1: value1, ...)`
- **string** - the default, any values that do not conform to the above 2 formats

A variable value is parsed as if it were an OtterScript string, where the same parsing rules apply, primarily that runtime variables are evaluated.

Because of this, a value can be escaped with the backtick character (i.e. `` ` ``) if the value is meant to be a literal string but happens to conform to the format of one of the other types, for example: `` ` `` ``@(some attribute)`` will resolve to a literal string `@(some attribute)` instead of a list containing a single element `some attribute`.

## Permissions {#permissions data-title="Permissions"}

Variable permissions are dependent upon the context they are edited and are evaluated eagerly such that if the qualification of an earlier rule is met, the consequence is validated and processing ends. The rules are as follows:

1. If a release is in context, then `Releases_Manage` is required
2. If a server or environment is in context, then `Infrastructure_Manage` is required
3. If none of the previous qualifications match, then privileges are evaluated against `Projects_Manage` for the project in context

## Sensitive Variables {#sensitive data-title="Sensitive Variables"}

Configuration variables may be marked "sensitive" which means that their values are only generally visible with manage permissions. This is **not** to be used for security purposes, only to replace the value on view-only pages with *(hidden)*. However, anyone that is able to edit a deployment plan could simply call `Log-Information $SensitiveVariable;` to view the value.

The secure option for storing passwords, connection strings, or other values intended to be encrypted is [Resource Credentials](/support/documentation/hedgehog/global-components/resource-credentials), as they require special permissions to manage,
and (as per Inedo Extensibility guidelines) should *never* be written to any logs.

## JSON Format & Bulk Editing {#json-format data-title="JSON Format & Bulk Editing"}

Variables at specific scopes can be edited in bulk as a JSON document at any of the scopes as defined in the [resolution rules](#resolution-rules) except for Execution and Promotion. The bulk JSON files are defined as a single root object with property/value pairs in one of two formats, either strings for both name and value, or a string name and *variable object* value. String values in both cases follow the [variable types](#variable-types) format above and are not JSON arrays/maps.

#### Full JSON Example {#full-JSON data-title="Full JSON Example"}

```
{
"myList": "@(this, is, a, list, another value)",
"myMap": "%(key: value, anotherKey: anotherValue, someList: @(a, b, c))",
"mySensitiveList": {
    "value": "@(hide, from, people, walking, behind, me)",
    "sensitive": true
},
"myString": "This is a simple string"
}
```
