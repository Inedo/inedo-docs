---
title: "For/Each Blocks"
order: 4
---

For-Each Blocks in OtterScript allow you to repeat a group of statements for each item in a list. They are useful for scenarios where you want to apply the same set of actions across multiple servers, files, or configuration values without duplicating code.

### Basic Usage

A For Each Block loops over a collection — like an array, function result, or a generated list — and executes the enclosed statements once for each item.

In the following examples:
* `@ServiceNames` is a list of service names.
* The `Ensure-Service` operation is run once for each service.

### In Text Editor

```bash
foreach $ServiceName in @ServiceNames
{
    Ensure-Service $ServiceName
    (
        StartupType: Automatic,
        EnsureRunning: true
    );
}
```

### In Visual Editor:

* Add a For Each block.
* Specify the Loop Variable and Collection.
* Add statements inside the loop body that will execute for each item.

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

### For Each Best Practices
* Use meaningful loop variables: `$App`, `$Server`, `$ServiceName` make the loop easier to read.
* Keep loops simple: Avoid overly complex logic inside loops when possible.
* Watch for empty lists: If the list is empty, the block will simply not run — no errors will occur.