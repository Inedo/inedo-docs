---
title: Comments & Descriptions
keywords: otterscript, otter, hedgehog, buildmaster
show-headings-in-nav: true
sequence: 20

---
Unlike most general-purpose programming languages, OtterScript is designed to be both visualized and coded. As such, most comments are not ignored by the compiler -- they actually are an important part of the plan that the script is describing.

## Statement Descriptions {#statement-descriptions data-title="Statement Descriptions"}

All of the comments that precede a statement are considered to be that statement's *Description*. The descriptions is used as follows:

{.docs}
- **Visual Editor Blocks** - when viewing a plan, either in the editor or in the web application, "block" statements use the first line of the description as the "Short Description", and the remaining lines as the "additional comments". These are displayed in different font sizes.
- **Visual Editor Statements** - all other statements (with the exception of Action statements) treat the description as an "additional comment", which means it's displayed with the same font style.
- **Log Scoping** - if a "block" statement has description, the first line will be used as the log scope.

## Additional Headers {#additional data-title="Additional Headers"}

All of the comments at the beginning of an OtterScript that begin with "##AH:" are considered to be Additional Headers, which essentially serve as key/value pair metadata for the plan. These are not incorporated into the first statement's description.

Currently, the only recognized header key is "UseTextMode", which instructs the plan editor to always open in text mode. You will see this added when switching between text and visual modes.

::: {.upcoming}
In a future version of BuildMaster, Otter, and Hedgehog, `EvaluateStatement Descriptions` will be supported, allowing variables and variable functions to be used in block comments.

```
##AH:EvaluateStatementDescriptions
foreach $database in @DatabasesToUpdate
{
# Update Database - $database
{
# …
}
}
```
:::

In future releases, Additional Headers could be used for all sorts of things, such as:

{.docs}
- Insert name of person who last edited the plan
- [Indent style](https://en.wikipedia.org/wiki/Indent_style) - curly brace formatting
- Casing preferences - whether to use PowerShell-Style or bash_style operation names
- Preventing any debug messages from being logged

Don't hesitate to [contact us](/contact) with ideas or if you need one of these for your organization.

## Other Comments {#best-practices data-title="Other Comments"}

Comments made within a statement are ignored by the compiler, which means that they will not be preserved when switching between text and visual modes in the plan editor. For example, consider the following script.

```
#Ensure HDars
#Creates the Hdars AppPool and Site if it doesn't exist
{
    IIS::Ensure-AppPool HDarsAppPool (
        Pipeline: Integrated,
        Runtime: v4.0
    );
    IIS::Ensure-Site HDars (
        AppPool: HDarsAppPool,
        Path: $WebsiteRoot,
        Protocol: http,
        # bind to port 1000 on all IPs, any hostname
        Binding: *:1000:
    );
}
```   

Log messages from the operations will be under a "Ensure HDars" log scope, and the plan editor will display the "block" with a "Ensure HDars" name the following line as a comment.

However, the comment preceding the "Binding" argument will be ignored by the compiler, and lost when switching modes. That comment could be preserved by placing it before the Ensure-Site operation.

### Comment Syntax {#common data-title="Common Syntax"}


Two forms of comments are supported: single-line comments and delimited comments. *Single-line comments* start with the characters `//` or the character `#` and extend to the end of the source line. *Delimited comments* start with the characters `/*` and end with the characters `*/`. Delimited comments may span multiple lines.

Comment forms will not be preserved when switching between text and visual modes.
