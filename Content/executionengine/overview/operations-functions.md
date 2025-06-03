---
title: "Operations & Functions"
order: 1
---

## Operations
Operations are the tasks that the execution engine will run on a server. The Inedo Core extension includes many built-in operations, and you can add more using the Inedo SDK. To see a list of all of our extensions, visit the [Extensions Feed](https://proget.inedo.com/feeds/Extensions?_ga=2.261820238.2141482059.1613971684-2090252612.1609900297). Some operations are [simple](/docs/otter/reference/operations/general/sleep), and some are [complex](/docs/otter/reference/operations/general/transfer-files). Through a combination of operations, scripts, variables, and blocks, you will be able to model any script to deploy an application to an environment.

### Using an Operation in a Script
An operation has the follow parts:

```
Namespace::Name [PositionalArgument]
(
    NamedArgument1: value,
    NamedArgument2: value,
    ...
    OutputArgument1 => $variable
)
```
The **Namespace** component is optional, and is often the same name as the extension which defines the operation. It may be specified if there is any ambiguity due to multiple operations with the same name, or just to increase clarity in a plan.

An operation's **positional argument** usually aliases one of the named arguments to provide a shorthand way to perform a common task. For example, the following two usages of the Sleep operation are equivalent:

```
Sleep(Seconds: 10);
Sleep 10;
```
Operations use **output arguments** to store information to runtime variables in the calling plan.

## Functions
Functions are used just like variables in OtterScript, except they cannot be assigned to, and may accept arguments. Parameterless functions may also be referred to as global or system variables.

Like Operations, many functions are included in the Inedo Core extension, and custom functions can be created using the Inedo SDK.

### Function Usage  
Functions may be used anywhere an OtterScript variable may be used. Just like a variable, a function will be evaluated and replaced when it is used in a string literal. A function is invoked as follows:
```
$TextReturningFunction(arg1, arg2, ...)
@ListReturningFunction(arg1, arg2, ...)
%MapReturningFunction(arg1, arg2, ...)
```
Functions can take zero or more arguments. Unlike operations, function arguments are always positional; argument names are only included for documentation purposes.

### Variable Aliasing
When a function accepts zero arguments, it may be used either with or without parentheses (like a variable). For example, `$Func()` is equivalent to `$Func`. For this reason, parameterless functions are sometimes referred to as global or system variables in documentation.

If, however, there is a user-defined configuration variable or runtime variable of the same name as a parameterless function, then this variable will take precendence. To unambiguoulsy specify the function instead, the `$Func()` syntax may be used instead. 

"Shadowing" a function like this is not recommended, and is only intended to prevent new functions from changing the behavior of existing script, should any variables happen to have the same name.