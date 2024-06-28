---
title: "OtterScript Reference"
order: 4
---

![otterscript.svg](/resources/docs/otterscript.svg){height="" width=""}

*OtterScript* is a Domain-Specific Language that was designed in tandem with the [execution engine](/docs/otter/scripting-in-otter/otter-otterscript-and-operations) to represent [configuration plans](/docs/otter/scripting-in-otter/otter-otterscript-and-operations#configuration) and [orchestration plans](/docs/otter/scripting-in-otter/otter-otterscript-and-operations#orchestration) in Otter, and deployment plans in BuildMaster. Because a DSL is inherently limited in functionality, a key feature of is the ability to "drop down" to a [lower-level scripting language](/docs/otter/scripting-in-otter/otter-scripting-powershell) as needed.

While OtterScript is neither a general-purpose programming language (like Ruby), nor a general-purpose mark-up language (like YAML), it was inspired by both, and allows you to build declarative and imperative plans.

:::info (INFO)
**Shared Documentation Note** - instead of copy/pasting the documentation (or creating a whole new set of documentation for OtterScript), we've kept the OtterScript detail pages in the Various Documentation, and the very few things that are Otter-specific are noted on these pages:

- [Comments & Descriptions](/docs/executionengine/otterscript/comments-and-descriptions)
- [Formal Grammar](/docs/executionengine/otterscript/overview)
- [Formal Specification](/docs/executionengine/reference/formal-specification)
- [Strings & Values](/docs/executionengine/otterscript/strings-and-literals)
:::


## Language Design Goals 

In addition to implementing the *actual* requirements of an advanced execution engine capable of executing across thousands of servers, we built OtterScript with these factors in mind:

- easy to learn for those with little to no programming experience
- familiar and intuitive to those with intermediate programming experience
- extensible and powerful for those with programming and domain expertise

In fact, using the Visual Mode in the plan editor, many users may not even realize they're building plans using *OtterScript*.

![otter-text.gif](/resources/docs/otter-text%281%29.gif){height="" width=""}

As you become more familiar with the execution engine, this reference section will help you accomplish even more things using Otter.