---
title: "Loop Blocks"
order: 2
---

Looping over the same series of statements can be useful for both visualization and removing repetitive statements in a plan. That's where the `foreach` Block comes in.

`foreach` runs the set of statements in the block while enumerating over a list. The List can be a variable, a function, or a literal expression. 

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

Available in BuildMaster `6.1.11+` and Otter `2.2.5+`.

### `with credentials`

Runs the statement block as a different user (credential must be defined):

```bash
with credentials=admin-user
{
    Log-Information "Running as admin.";
}