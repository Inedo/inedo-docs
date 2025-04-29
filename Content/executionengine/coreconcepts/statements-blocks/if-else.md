---
title: "If/Else Code Blocks"
order: 3
---

If/Else Blocks in OtterScript provide conditional logic, allowing your plans to execute different sets of statements depending on whether certain conditions are true or false.

This makes your plans flexible and reusable across different environments, servers, and deployment scenarios.

## Basic Usage

An If/Else Block evaluates a condition:
* If the condition is `true`, the statements inside the `If` section are executed.
* Otherwise, the statements inside the optional `Else` section are executed.

In these examples: 
* On production servers, a certificate is installed.
* On other servers, a log message is recorded instead.

### In Text Mode

```bash
if $EnvironmentName == "Production"
{
    Install-Certificate
    (
        Path: $PathCombine($CertificateRoot,"prod-cert.pfx")
    );
}
else
{
    Log-Information "Certificate installation not required in non-production environments.";
}
```

### Visual Mode

If you're working in Visual Mode, If/Else blocks are easy to insert:
* Add an If/Else block.
* Define the Condition expression.
* Populate the `If` and optional `Else` sections with statements.

## Writing Conditions

Conditions inside If blocks must be Boolean expressions — they should evaluate to either true or false.

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

Supported logical operators include:
* `&&` (AND)
* `||` (OR)
* `!` (NOT)

## Nesting If/Else Blocks

You can nest If/Else blocks inside each other for complex decision trees:

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

## Common Scenarios
* Environment-specific configurations: Deploy different assets, apply settings, or run checks depending on the environment (Production, QA, Development).
* Server-specific behavior: Perform actions based on server roles or operating systems.
* Feature toggles: Enable or disable features based on runtime flags.

## Best Practices
* Keep conditions simple and readable. Prefer short, clear expressions over deeply nested logic where possible.
* Comment your conditions if the logic isn't immediately obvious.
* Use Else blocks sparingly when a default action is truly needed.