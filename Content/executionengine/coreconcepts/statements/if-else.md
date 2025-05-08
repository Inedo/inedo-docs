---
title: "If-Else Statements"
order: 3
---

`if-else` Statements in OtterScript function like typical "if-else" statements found in other languages, allowing your plans to execute different sets of statements based on conditional logic.

#### Common `if-else` Usage
* Environment-specific configurations: Deploy different assets, apply settings, or run checks depending on the environment (Production, QA, Development).
* Server-specific behavior: Perform actions based on server roles or operating systems.
* Feature toggles: Enable or disable features based on runtime flags.

:::(info) (`if-else` Example:)
A typical `if-else` statement in OtterScript:

```bash
if $EnvironmentName == "Production"
{
    PSExec Import-Certificate Filepath "C:\files\intermediate.cert";
}
else
{
    Log-Information Certificate not needed in $EnvironmentName;
}
```
:::

## Writing Conditions

Conditions inside `if` statements must be Boolean expressions — they should evaluate to either `true` or `false`.

Common expressions:

| Example | Description |
|---|---|
| `$EnvironmentName == "Production"` | Check if an environment name matches |
| `$ServerRole == "WebServer"` | Verify a server role |
| `$IsWindows == true` | Check operating system type |
| `@FeatureFlags.Contains("EnablePayments")` | Check if a list contains a value |

You can combine conditions using logical operators:

```bash
if $EnvironmentName == "Production" && $IsWindows
{
    // Only runs on production Windows servers
}
```

Supported logical operators in OtterScript include:
* `&&` (AND)
* `||` (OR)
* `!` (NOT)

## Nesting if-else Blocks

OtterScript lets you nest `if-else` statement blocks inside each other for complex decision trees:

```bash
if $EnvironmentName == "Production"
{
    if $ServerRole == "Database"
    {
        Log-Information "Running Production Database Configuration...";
    }
}
else
{
    Log-Information "Non-production environment setup.";
}
```
