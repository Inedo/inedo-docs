---
title: Configuration Variables
keywords: otter,executions
show-headings-in-nav: true
---

Configuration variables are name/value pairs that you define on servers, roles, environments, and globally. You can reference these variables from within your plan, and the execution engine will automatically resolve the value based on the context of the execution.

:::attention {.best-practice}
![](/resources/images/icons/best-practices.png)

For example, if you were to define a `$WebsitesRoot` variable on `HDARS-WEBSV1` with a value of `C:\Websites`, and a `$WebsitesRoot` variable on `HDARS-WEBSV2` with `D:\Apps\web`, when you install a package to `$WebsitesRoot\HDars` within your plan, the execution engine will automatically resolve the variable based on the server in context (i.e. the server the package is being installed on).
:::

## Defining Configuration Variables {#defining-variables data-title="Defining Variables"}

You can define variables by going to the desired context (such as the specific server or role), and then either adding a single one, or bulk-editing the set in JSON.

<tab-block>
    <tab name="Add Variable">
        <img class="screenshot" src="/resources/documentation/otter/2/create-variable.png" alt="Add Variable" />
    </tab>
    <tab name="Bulk-editing">
        <img class="screenshot" src="/resources/documentation/otter/2/bulk-variable.png" alt="Bulk import" />
    </tab>
</tab-block>

OtterScript supports [three types of variables](/docs/various/execution-engine/otterscript/strings-and-literals), and if your variable's value starts with `@(` or `%(`, then it will be treated as a list or map type, respectively.  For example, `@(item1, item2, item3)` will be considered a list variable, `%(key1: value1, key2: @(list1, list2)` a map variable, and `@((` an invalid expression.

As such, you do not need to specify prefix (&#36;,&#64;, or &#37;) for your variables.

:::attention {.technical}
**Do not** store sensitive information in variables; use [credentials instead](/docs/otter/global-components/resource-credentials). While there is an option to "obscure the value of this variable from casual viewing", this does exactly as the name implies, and is not intended to be used as a security feature.
:::

## Cascading Behavior {#cascading data-title="Cascading Behavior"}

Configuration Variables cascade, which means that you can define a variable of the same name in multiple places, and the correct value will be resolved at runtime. For example, if you define a variable named `$HDarsPath` on the `Testing` environment, when an orchestration or configuration plan runs against a server in the `Testing` environment, that variable will resolve at runtime.

If you also defined `$HDarsPath` on a single server in the `Testing` environment, then *that* value would be used instead.

This allows for reusing plans and templates without having to change local variables.

### Resolution Rules {#rules data-title="Resolution Rules"}

The variable definition that's the "closest" match to the current context is used. This is determined as follows:

{.docs}
- Job
- Server
- Environment
- Server Role
- Global

### Multiple Scopes {#scopes data-title="Multiple Scopes"}

You can also assign multiple scopes to a configuration variable; for example, you could define a variable that's associated with *both* the `Testing` environment and the `hdars-web` role. A multi-scope variable simply adds precedence to the highest-scope ([Job](/docs/otter/core-concepts/jobs) is still "closer" than a server or server role).

However, this can become confusing as the resolution rules are not intuitive. We generally discourage this use. You can only create multi-scoped variables from the administration section, and they are visible (but not editable) on the servers, server roles, etc., they are associated with.
