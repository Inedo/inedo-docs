---
title: "Text Templating"
order: 2
---

Templating allows you to combine plain text, variables, and code to render a desired output, such as a configuration file or an email message. Some operations have special handling for templating, and there is a special operation that allows templates to be resolved to a variable to be used anywhere in a plan.

## Templating Syntax
There are two different template syntaxes:

* The **simple** syntax is used by default, and works just like regular OtterScript strings and values in that it allows for scalar variable expression replacement. For most use cases, this is adequate. Essentially, this is just the normal variable expansion that happens in OtterScript string literals or the `eval` function.

* The **full** syntax adds support for tags that allow you to add branches and loops to your template. This syntax is only available where it is explicitly documented as supported (such as the `Apply-Template` operation).

### Simple Syntax
Because the Simple syntax uses the same processing engine as regular string replacement, the OtterScript strings and values documentation covers the syntax.

### Full Syntax
The full syntax uses the same string processing engine for variable replacements, but it is first preprocessed into OtterScript. A tag is a code fragment between a "<%" and a "%>" string sentinel. Only two grammar elements are supported in templates: Iteration Block and Predicate Statement. A tag can be escaped using the standard OtterScript escape character \` (grave apostrophe). For example: <\`%

Note that tags are considered first when processing a template, so even when passing a literal to a function, the tag must be escaped. For example: $func(<\`%)

A closing tag does not need to be escaped, but it is not an error to do so.

Following are some examples of the full syntax.

Email Message:
```
<p>Hi $HtmlEncode($name)!</p>
<p>
Just letting you know that the $HtmlEncode($EnvironmentName)
environment contains these servers:
</p>
<% if $ListCount(@ServersInEnvironment) = 0 { %>
<p>none</p>
<% } else { %>
<ul>
<% foreach $s in @ServersInEnvironment { %>
<li>$HtmlEncode($s)</li>
<% } %>
</ul>
<% } %>
<p>Hope you find this useful!</p>
```

Config File:
```
<p>Hi $HtmlEncode($name)!</p>
<p>
Just letting you know that the $HtmlEncode($EnvironmentName)
environment contains these servers:
</p>
<% if $ListCount(@ServersInEnvironment) = 0 { %>
<p>none</p>
<% } else { %>
<ul>
<% foreach $s in @ServersInEnvironment { %>
<li>$HtmlEncode($s)</li>
<% } %>
</ul>
<% } %>
<p>Hope you find this useful!</p>
```



## Apply-Template Operation
All templating functions can be performed using the `Apply-Template` operation. For convenience, a few other operations may include direct support for full templating as well (such as the `Send-Email` operation), and the documentation will indicate that full templating syntax is supported in these cases.

The `Apply-Template` operation is flexible in terms of its inputs and outputs. It can process a template stored in any of the following:

* **asset** - a named text template asset stored in a raft

* **literal** - literal text embedded directly in the OtterScript

* **file** - a file relative to the working directory of the server in context

You may also specify where to store the resulting text:

* **variable** - a runtime variable is created containing the output

* **file** - a file relative to the working directory of the server in context (this may be the same as the input file to overwrite)

Following are some examples of the `Apply-Template` operation in OtterScript:

Asset:
```
Apply-Template MyTemplateAsset
(
    OutputVariable => $result,
    AdditionalVariables: %(name: Steve, number: 1000)
);
```

Literal: 
```
Apply-Template
(
    Literal: >>Hi $name,
    This is just a test.
    >>,
    OutputVariable => $result,
    AdditionalVariables: %(name: Steve, number: 1000)
);
```

File:
```
Apply-Template
(
    InputFile: MyWebConfigTemplate.config,
    OutputFile: C:\Websites\HDARS\Web.config,
    AdditionalVariables: %(name: Steve, number: 1000)
);
```