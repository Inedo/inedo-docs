---
title: "OtterScript Overview"
order: 1
---

![Otter Script](/resources/docs/otterscript.png){height="" width="50%"}

_OtterScript_ is a Domain-Specific Language that was designed in tandem with the execution engine to represent [configuration plans](/docs/otter/scripting-in-otter/otter-otterscript-and-operations#configuration) and [orchestration plans](/docs/otter/scripting-in-otter/otter-otterscript-and-operations#orchestration) in Otter, and [deployment plans](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts) in BuildMaster. Because a DSL is inherently limited in functionality, a key feature of OtterScript is the ability to "drop down" to a [lower-level scripting language](/docs/otter/scripting-in-otter/otter-scripting-powershell) as needed.

While OtterScript is neither a general-purpose programming language (like Ruby), nor a general-purpose mark-up language (like YAML), it was inspired by both, and allows you to build declarative and imperative plans.

## Language Design Goals

In addition to implementing the _actual_ requirements for an advanced execution engine capable of running on thousands of servers, we considered these factors when developing OtterScript:

* easy to learn for those with little to no programming experience
* familiar and intuitive for those with intermediate programming experience
* extensible and powerful for those with programming and technical expertise

By using the Visual Mode in the plan editor, many users won't even realize that they're creating plans with _OtterScript_.

![Visual Mode](/resources/docs/otter-text.gif){height="" width="50%"}