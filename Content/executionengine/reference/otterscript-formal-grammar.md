---
title: "Formal Grammar"
order: 2
---

The following formal grammar defines a valid OtterScript using a [BNF-like convention](#conventions). This guide may be useful for writing syntax highlighters or script validators. See the [formal specification](/docs/executionengine/reference/formal-specification) to learn how these behave specifically.

```
<otter_script> ::=
  [<global_variable_declaration>] [..n]
  [<scoped_statement_block>] [..n]
                                                                                         
<global_variable_declaration> ::=
  global /variable_expression/ = /literal_expression/;
                                                                                                                                                                                                                                                                      
<scoped_statement_block> ::=
  [<named_module>] [..n]
  [<statement>] [..n]

<statement> ::=
  <action_statement> | <anonymous_block_statement> | <assign_variable_statement> | 
  <await_statement> | <call_module_statement> | <context_iteration_statement> | 
  <error_statement> | <execution_directive_statement> | <fail_statement> | 
  <iteration_block_statement> | <log_statement> | <predicate_statement> | 
  <set_context_statement> | <throw_statement> | <try_statement> | <warn_statement> |
  <force_normal_statement>

<action_statement> ::=
  [/namespace/::]/operation_name/ [/literal_expression/] ["("
     ( (/param_name/: /literal_expression/) |
       (/param_name/=> /variable_expression/) )
     [,...n]
  ")"];                                                                                        
                                                                                         
<anonymous_block_statement> ::=
  { <scoped_statement_block> }

<assign_variable_statement> ::=
  set [local | global] ( /variable_expression/ | /indexed_expression/ ) = /literal_expression/;

<await_statement> ::=
  await [/async_token/];

<break_statement> ::=
  break;

<call_module_statement> ::=
  call [/raft_name/::]/module_name/ ["("
     ( (/param_name/: /literal_expression/) |
       (/param_name/=> /variable_expression/) )
     [,...n]
  ")"];

<context_iteration_statement> ::=
  foreach /context_type/ in /vector_expression/ {  <scoped_statement_block> }

<continue_statement> ::=
  continue;

<error_statement> ::=
  error;

<execution_directive_statement> ::=
  with <execution_directive> [,...n]
  { <scoped_statement_block> }

<execution_directive> ::=
  retry=<integer> |
  timeout=<integer> |
  executionPolicy=(always|onChange) |
  async[=/async_token/] |
  lock[=[!]/lock_token/] |
  isolation |
  credentials=<userNamePasswordCredentials>

<fail_statement> ::=
  fail;

<iteration_block_statement> ::=
  foreach /variable_expression/ in /vector_expression/ {  <scoped_statement_block> }

<log_statement> ::=
  Log-(Debug|Information|Warning|Error) /literal_expression/;

<force_normal_statement> ::=
   force normal;

<predicate_statement> ::=
  if <predicate_expression> { <scoped_statement_block> }
  [ else { <statements> } ]
  
<return_statement> ::=
  return;

<set_context_statement> ::=
  for /context_type/ /literal_expression/ { <scoped_statement_block> }

<throw_statement> ::=
  throw [/literal_expression/];

<try_statement> ::=
  try { <scoped_statement_block> }
  catch { <scoped_statement_block> }

<warn_statement> ::=
  [force] warn;

<named_module> ::=
  module /module_name/ 
  ["<" 
     ([out] /module_param_name/[=/param_default/])
     [,...n] 
  ">"] { <statements> }
```

Grammar Elements
----------------

The grammar refers to the following elements:

|  Element | Details |
| --- | --- |
| `any_name`<br> `namespace`<br> `operation_name`<br> `param_name`<br> `module_name` | No more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), and underscores (_); must start with a letter, and may not start or end with a dash or underscore |
| `async_token` <br> `lock_token` | No more than fifty characters: numbers (0-9), upper- and lower-case letters (a-Z); must start with a letter. We are considering changing this to allow variable expansion; see <span style="background-color: #B8B9C1"> *(proposed)* IEE-21</span>, and let us know if this would be helpful. |
| `module_param_name`	 | A variable type identifier ($, @, or %) immediately followed by `any_name` |
| `context_type` | Either `server`, `role`, <span style="background-color: #F6A52B">*`(BuildMaster only)`* `deployable`</span>, or `directory` |
| `param_default` | An implicit or quoted string |
| `comment` | See [Comments & Descriptions](/docs/executionengine/otterscript/comments-and-descriptions){target="_blank"} |
| `variable_expression` | A variable type identifier ($, @, or %) immediately followed by one of: <br><ul><li> simple name - follows same rules as `any_name`<br><li> explicit name - a left curly brace (`{`), followed by of characters with the same rules as any_name but that also allow spaces, followed by a right curly brace (`}`)</ul> |
| `literal_expression` | a `scalar_expression`, `vector_expression`, or `map_expression` |
| `scalar_expression` | a string; see [Strings and Literals](/docs/executionengine/otterscript/strings-and-literals){target="_blank"} |
| `indexed_expression` | A `variable_expression` for a vector (@) or map (%) type, immediately followed one of: <br><ul><li> left bracket ([), scalar_expression, then right bracket (]) <br><li> dot (.) then scalar_expression</ul> |
| `vector_expression` | one of: <br><ul> <li> a `variable_expression` for a vector (@) type, or a <br> <li> a @ character followed by left-parens ((), and any number of `literal_expression` delimeted with commas (,), followed by a right-parens ()) </ul> |
| `map_expression` | one of: <ul>  <li> a `variable_expression` for a map (%) type, or a <br>  <li> a % character followed by left-parens (`(`), and any number of key-values pairs delimeted with commas (`,`), followed by a right-parens (`)`). A key-value pair consists of `any_name`, followed by a colon (`:`), followed by a `literal_expression` </ul> |


Grammar Conventions
-------------------

The following conventions are used to describe OtterScript's grammar, and are used in documentation and examples:


| Convention | Details |
| --- | --- |
| `<label> ::=` | 	The name for a block of syntax. This convention is used to group and label sections of lengthy syntax or a unit of syntax that can be used in more than one location within a statement. Each location of the block of syntax can be used is indicated with the label enclosed in chevrons: `<label>`. |
| Text | Undercoated text is an OtterScript keyword. |
| `( )`(parens) | Syntactical grouping for lists and optional items. Do not type the parens. |
| [,...n] | Indicates the preceding item can be repeated n number of times. The occurrences are separated by commas. |
| [...n] | Indicates the preceding item can be repeated n number of times. The occurrences are separated by blanks. |
| `[ ]` (brackets) | Optional syntax items. Do not type the brackets. |
| `|` (vertical bar) | Separates syntax items enclosed in brackets or braces. You can use only one of the items. |
| `" "` (quotes) | Required syntax item, used for parens, brackets, etc. Do not type the quotes. |
| `/ /` (slashes) | Required grammar element. |
