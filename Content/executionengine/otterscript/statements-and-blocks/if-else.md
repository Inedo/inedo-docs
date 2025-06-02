---
title: "If / Else Blocks"
order: 4
---

If / Else blocks allow for greater flexibility when creating plans, and reusing plan blocks in multiple situations.

For example, maybe a security certificate is only needed on production servers, so an If/Else block can be inserted so that if the server is in the Production environment, the security certificate will be installed via PowerShell.

:::(info) (`if/else` Example:)
A typical `if/else` statement in OtterScript:

```bash
if $myEnvironment == "Production"
{
    Log-Information "Environment is Production.";
}
else
{
    Log-Information "Environment is not Production.";
}
```
:::

## Writing Conditions

Conditions inside `if` statements must be Boolean expressions —that evaluate to either `true` or `false`.

OtterScript uses strict Boolean logic. Unlike languages like JavaScript or PowerShell, it does not use truthy or falsy values — expressions like if `"hello"` or `if 0` are invalid. Only `true` or `false` (or variables that explicitly hold Boolean values) are allowed in conditions.

✅ Valid: `if $isEnabled`
❌ Invalid: `if $someString` or `if $count` (where `$count` is a number)

### Common expressions:

| Example | Description |
|---|---|
| `$foo == "bar"` | Equality check |
| `$myFlag == true` | Boolean comparison |
| `$myList.Contains("abc")` | Membership test |
| `$count > 3` | Numerical comparison |
	
### Logical Operators	
	
You can combine conditions using:

* `&&` (AND)
* `||` (OR)
* `!` (NOT)

Example: 

```bash
if ($myEnvironment == "Production" && $isEnabled)
{
    Log-Information "Environment is Production and the feature is enabled.";
}
```

You can wrap expressions in parentheses for clarity and precedence:

```bash
if (($myEnvironment == "Staging") || ($myRole == "WebServer"))
{
    Log-Information "Either the environment is Staging or the role is WebServer.";
}
```

## Booleans in OtterScript

Boolean variables are commonly used in conditionals. You can write:

```bash
if $isEnabled
```
or

Which is equivalent to:

```bash
if $isEnabled == true
```

OtterScript emphasizes **declarative logic**, so conditionals often reflect current environment state (e.g., `$Environment == "Production"`), rather than being procedural logic blocks.

:::(info) (Variable Case Sensitivity)
Variables in OtterScript are not case sensitive, therefore:

```bash
if $IsEnaBled
```

Is equivalent to:

```bash
if $isEnabled
```
:::

## Nesting if/else Blocks

OtterScript lets you nest `if/else` statement blocks inside each other for complex decision trees:

```bash
if $myEnvironment == "Production"
{
    if $myRole == "Database"
    {
        Log-Information "Environment is Production and role is Database.";
    }
    else
    {
        Log-Information "Environment is Production but role is not Database.";
    }
}
else
{
    Log-Information "Environment is not Production.";
}
```

## if/elseif/else Behavior in OtterScript

OtterScript does not have a native `elseif`. To write `if/elseif/else` logic, you simply nest if statements inside else blocks:

```bash
if $myEnvironment == "Production"
{
    Log-Information "Environment is Production.";
}
else
{
    if $myEnvironment == "Staging"
    {
        Log-Information "Environment is Staging.";
    }
    else
    {
        Log-Information "Environment is neither Production nor Staging.";
    }
}
```