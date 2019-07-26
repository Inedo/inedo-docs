---
title: OtterScript Reference
keywords: otter,otterscript,configuration-plans
---

<style>
    .info.otter {
        border-color: #9163aa;
        color: #9163aa;
    }
</style>

![](/resources/images/logos/otterscript.svg){width="250"}

*OtterScript* is a Domain-Specific Language that was designed in tandem with the [execution engine](/docs/otter/execution-engine/overview) to represent [configuration plans](../core-concepts/plans#configuration) and [orchestration plans](../core-concepts/plans#orchestration) in Otter, and deployment plans in BuildMaster. Because a DSL is inherently limited in functionality, a key feature of is the ability to "drop down" to a [lower-level scripting language](../modeling-infrastructure/powershell) as needed.

While OtterScript is neither a general-purpose programming language (like Ruby), nor a general-purpose mark-up language (like YAML), it was inspired by both, and allows you to build declarative and imperative plans.

:::info {.otter}
**Shared Documentation Note** - instead of copy/pasting the documentation (or creating a whole new set of documentation for OtterScript), we've kept the OtterScript detail pages in the Various Documentation, and the very few things that are Otter-specific are noted on these pages:

{.docs}
- [Comments & Descriptions](/docs/various/execution-engine/otterscript/comments-and-descriptions)
- [Formal Grammar](/docs/various/execution-engine/otterscript/formal-grammar)
- [Formal Specification](/docs/various/execution-engine/otterscript/formal-specification)
- [Strings & Values](/docs/various/execution-engine/otterscript/strings-and-literals)
:::


## Language Design Goals {#design-goals}

In addition to implementing the *actual* requirements of an advanced execution engine capable of executing across thousands of servers, we built OtterScript with these factors in mind:

{.docs}
- easy to learn for those with little to no programming experience
- familiar and intuitive to those with intermediate programming experience
- extensible and powerful for those with programming and domain expertise

In fact, using the Visual Mode in the plan editor, many users may not even realize they're building plans using *OtterScript*.

![Plan Editor](/otter/otter-text.gif){.screenshot}

As you become more familiar with the execution engine, this reference section will help you accomplish even more things using Otter.
