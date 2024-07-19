---
title: "Strings & Values"
order: 3
---

﻿ <title>Strings & Values</title> <meta content="text/html; charset=utf-8" http-equiv="Content-Type"> <meta name="keywords" content="otterscript,variables,otter,buildmaster,hedgehog"> <meta name="show-headings-in-nav" content="true"> <meta name="sequence" content="30"> <style>.upcoming.proposed::before{ content:'proposed:' }</style> 

Values -- i.e. the desired states, settings, and values of the things that need to be configured on servers -- are a first-class concept in OtterScript, and are represented through a _literal expression_. There are three types of literal expressions:

*   **Map expression** - represents a map, such as %( name: value, name2: value2 )
*   **Vector expression** - represents a list/vector, such as @( value1, value2, value3 )
*   **Scalar expression** - all other literal expressions

The [formal grammar](/docs/executionengine/reference/otterscript-formal-grammar) describes _exactly_ how map and vector expressions should be formated and where they may be used.

## Automatic Variable Expansion

All statements persist their contained data and values as a string, and it's only during execution of the statement that the expression is _evaluated_. It's during this evaluation that variables values are expanded.

When a literal expression is evaluated, any contained scalar variable expressions (e.g. `$SomeVariable`) are replaced with the corresponding value, using the following method:

*   If the expression represents a function (e.g. `$SomeFunc()`), then a variable function with that name is invoked using the specified parameters
*   Otherwise, a value is located in this priority
    1.  Runtime variable
    2.  Configuration variable
    3.  Variable function
    4.  Raft variable

When a function is invoked, the parameters are evaluated prior to execution; for example, `$PathCombine($a,$b)` will first resolve $a and $b before invoking PathCombine

### Indexed List or Map Expansion

Because map and list expressions represent multiple values, they cannot be automatically expanded into a single value (i.e. a string). You can, however, use an _indexing expression_ to identify a single value to expand within a map or a list. This works a lot like arrays or property indexers in other programming languages.

OtterScript supports both bracket-based indexing and dot-based indexing, and lists indexes are 0-based integers. For example:

*   `@MyList[3]` - will expand to the forth item in the list, or an error if @MyList doesn't have at least four items
*   `@(1,2,3)[zero]` - will result in a runtime error, because `zero` is not an non-negative integer
*   `@(MyList)[$i]` - will expand to whatever $i expands to, or a runtime error if $i is not a positive integer
*   `%MyMap[hdars]` - will expand to the value with a key of `hdars`, or result in a runtime error if that value doesn't exist or is not a scalar value
*   `%Config.Sys.Drive` - will expand to the `Drive`-keyed value in the `Sys`-keyed map within the `Config` map

#### Using Indexing in a String

Because the `@` and `%` characters are only recognized as variable indicators when used at the beginning of a literal expression, they must be wrapped with the nested evaluate expression when evaluation is intended anywhere else in the string:

*   `string with @var[1] value` - will **not** expand the `@var[1]` expression
*   `string with $(@var[1]) value` - **will** expand the `@var[1]` expression

This additional syntax means that @ and % characters do not need to be explicitly escaped; since indexing was added to OtterScript, this was required to maintain compatibility with older scripts. It is also practical, as @ and % characters are not that uncommon in text.

### Escaping Expansion

The grave apostrophe (```) serves as an escape character. The following can be escaped:

*   Variable indicator -``$`, ``@`, ``%`
*   Whitespace - ``n`, ``t`, ``r`
*   Escape character - ````

If any other character follows the grave apostrophe, then it will still be considered an escape character, but may raise a warning during compilation.

### Additional Expansion

The `$Eval(«arg»)` variable function will perform an additional variable expansion using the method described above for `«arg»`.

<div class="upcoming proposed">

### Avoid Expansion Altogether

Sometimes, it's desirable to avoid expansion all together, without needing to escape using the grave apostrophe. Some languages perform this with an additional decorator on a string literal, such as the `@` before a string in C#.

We're considering adding this using a `/literal` decorator at the _end_ of a quoted or swim string. For example, `"Not $escaped"/literal`.

If you have any interest in this, please [submit a ticket](https://my.inedo.com/tickets/new) with how you would plan to use this, and how it would be helpful; this is currently tracked as [IEE-20](https://issues.inedo.com/youtrack/issue/IEE-20), and we will link your ticket and seek to prioritize this.

</div>

## String literal syntaxes

To help contain values without worrying about escaping, OtterScript offers several syntaxes for string literals:

<table>

<tbody>

<tr>

<td>Implicit</td>

<td>any characters except newlines and the expected delimiter (which can be a comma, right-parenthesis, right-brance, or semi-colon, depending on context); surrounding whitespace is always trimmed</td>

</tr>

<tr>

<td>Quoted</td>

<td>any characters surrounded by a pair of single-or double-quotes; these cannot contain a newline character, and escaping sequences (such as \n\t) are not used</td>

</tr>

<tr>

<td>Swim</td>

<td>any characters surrounded by two identical fish sentinels; these are a pair of greater-than characters containing zero to five non-greater-than characters (>>, >--=>, >==8>); although not required, it's advised to make your fish sentinels look like fish as much as possible, as otters love fish</td>

</tr>

</tbody>

</table>

Following are some example strings:

<table>

<tbody>

<tr>

<td>`this is just an implicit string`</td>

</tr>

<tr>

<td>`"Quote string, with double quotes."`</td>

</tr>

<tr>

<td>`'A "quoted" string with single quotes.'`</td>

</tr>

<tr>

<td>`An "implicit" string because it doesn't start with a quote`</td>

</tr>

<tr>

<td>

<pre>>> this is a swim string
that can span multiple
lines, and contain "any' thing, except the 
closing fish >>
</pre>

</td>

</tr>

<tr>

<td>

<pre>>==8>
this is a more fish-looking sentinel; note that it
can this can contain >>, >--=>, or even a shark (>==|:>) 
because the surrounding fish have ==8 inside
>==8></pre>

</td>

</tr>

</tbody>

</table>

## Notes and Best Practices

Note that variable expansion gives the _appearance_ of assigning variables to operation parameters; this is not the case. It simply is a literal expression that will utilize variable expansion at runtime. As such, there is no need for things like string concatenation: just put the variables next to each other in the desired manner (e.g. `http://${host}/${path}`), and they will be evaluated at runtime as expected.

In general, describing your configuration with implicit strings will makes them easier to read and follow. We recommend using implicit strings when possible, quoted strings when you require a delimiter or non-trimmed whitespace, and fish strings when creating files or doing script-embedded operations.