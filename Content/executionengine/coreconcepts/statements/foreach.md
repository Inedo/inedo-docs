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
set @myList = @(A, B, C);

foreach $item in @myList
{
    Log-Information "Item: $item";
}
```

Output:

```plaintext
Item: A
Item: B
Item: C
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
    Log-Information "Number: $i";
}
```

This will output:

```plaintext
Number: 1
Number: 2
Number: 3
```

## For Statements in OtterScript { #for-statement }

OtterScript includes a `for` statement, but it is not a looping construct. For example:

```bash
for i = 1 to 100
```

Although this looks like a loop in other languages, it does not perform iteration in OtterScript. It's typically used for declarative bindings, not flow control. To iterate over a range of numbers, use:

```bash
foreach $i in @Range(1, 100)
```