---
title: "Foreach Statements"
order: 4
---

`foreach` Statements in OtterScript function like typical "foreach" statements found in other languages, allowing you to repeat a group of statements for each item in a list.

#### Common `foreach` Usage
* Bulk resource processing: Iterate over a list of servers, files, or database records to apply updates, validations, or configurations.
* Automated task execution: Run a specific action, like restarting services or clearing caches, across multiple nodes or components in a deployment.
* Data aggregation: Collect metrics, logs, or status reports from a set of resources for monitoring or reporting purposes.

:::(info) (`foreach` Example:)
A typical `foreach` statement in OtterScript:

```bash
foreach $ServiceName in @ServiceNames
{
    Ensure-Service $ServiceName
    (
        StartupType: Automatic,
        EnsureRunning: true,
        Name: script,
        Exists: true,
        DelayedStart: false,
        Path: "C:\Program Files\Hdars\Hdars.Service.exe" -arg1 -arg2
    );
}
```
:::

### Collections You Can Loop Over

You can loop over:

| Type | Example |
|---|---|
| List Variables | `@ServersInGroup(database-nodes)` |
| Built-in Functions | `@Range(1,5)` to loop over numbers 1 through 5 |
| Literal Arrays | `@(App1, App2, App3)` |

## Looping over Numbers

Use the built-in `@Range(start,end)` function to create numeric loops:

```bash
foreach $i in @Range(1,3)
{
    Log-Information "Loop iteration: $i";
}
```

This will output:

```bash
Loop iteration: 1
Loop iteration: 2
Loop iteration: 3
```

## Real-World Examples

### Deploy to Multiple Servers

```bash
foreach server in @ServersInGroup(web-nodes)
{
    Deploy-App
    (
        AppName: MyApp
    );
}
```

### Configure Multiple Applications

```bash
foreach $App in @(Accounts, Inventory, Billing)
{
    Configure-App
    (
        Name: $App
    );
}
```