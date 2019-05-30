---
title: OtterScript Overview
keywords: otterscript, otter, configuration-plans
sequence: 10
---

![](/resources/images/logos/otterscript.svg){width=50%}

*OtterScript* is a Domain-Specific Language that was designed in tandem with the
execution engine to represent [configuration plans](/support/documentation/otter/core-concepts/plans#configuration) and [orchestration plans](/support/documentation/otter/core-concepts/plans#orchestration) in Otter, and [deployment plans](/support/documentation/buildmaster/core-concepts/deployment-plans) in BuildMaster. Because a DSL is inherently limited in functionality, a key feature of is the ability to "drop down" to a [lower-level scripting language](/support/documentation/executionengine/components/powershell-and-shell) as needed.

While OtterScript is neither a general-purpose programming language (like Ruby), nor a general-purpose mark-up language (like YAML), it was inspired by both, and allows you to build declarative and imperative plans.  

## Language Design Goals {#language-design data-title="Language Design Tools"}

In addition to meeting implementing the *actual* requirements of an advanced execution engine capable of executing across thousands of servers, we built OtterScript with these factors in mind:

{.docs}
- easy to learn for those with little to no programming experience
- familiar and intuitive to those with intermediate programming experience
- extensible and powerful for those with programming and domain expertise

In fact, using the Visual Mode in the plan editor, many users may not even realize they're building plans using *OtterScript*.

![](/otter/otter-text.gif){width=100%}

  As you become more familiar with the execution engine, this reference section will help you accomplish even more things.
