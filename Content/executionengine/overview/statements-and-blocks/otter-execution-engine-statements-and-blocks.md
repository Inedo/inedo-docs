---
title: "Statements and Blocks"
order: 1
---

The execution engine interprets a [Plan](/docs/otter/scripting-in-otter/otter-otterscript-and-operations){target="_blank"} as a series of statements and blocks, in a top-down manner.

* **Statements** - most of the statements in your plans will be [Operations](/docs/otter/scripting-in-otter/otter-otterscript-and-operations#operations){target="_blank"}, but there are a few [Other Statements](/docs/executionengine/overview/statements-and-blocks/otter-execution-engine-statements-and-blocks-other-statements){target="_blank"} that can be used to build extremely advanced plans.
* **Blocks** - blocks contain statements and other blocks, and help with organization, flow, and error control; there are four block types:
    *[General Blocks](/docs/executionengine/overview/statements-and-blocks/otter-execution-engine-statements-and-blocks-general-blocks){target="_blank"}
    *[ Loop Blocks](/docs/executionengine/overview/statements-and-blocks/otter-execution-engine-statements-and-blocks-loop){target="_blank"}
    *[ Try /Catch Blocks](/docs/executionengine/otterscript/statements-and-blocks/try-catch){target="_blank"}
    [* If/Else Blocks](/docs/executionengine/overview/statements-and-blocks/otter-execution-engine-statements-and-blocks-if-else){target="_blank"}
    
If you're comfortable with programming or scripting, you'll probably already be familiar with how these blocks work. There is a formal specification available if you want to know precisely how the execution engine and plans/OtterScript work.

## Blocks
Blocks provide an easy way to organize your plans, and the sub-tasks those plans are made of. They can be nested as many levels deep as you need. All blocks provide variable scoping (i.e. if you set a variable in that block, it will only be available within that block) as well as log scoping.

## Log Scoping
Otter's logs were designed to be as easy to navigate and understand as plans. this is where collapsible log scopes come in:

**Plan**
![log-scoping-block.png](/resources/docs/log-scoping-block%282%29.png){height="" width=""}

**Execution Log**
![log-scoping-log.png](/resources/docs/log-scoping-log%281%29.png){height="" width=""}

If you give a block a **short description**, then the plan's execution log will have a scope of the same name. This way, as you nest statements and blocks in your plans, they will have parity in the log.

Because logs may contain sensitive data, such as the arguments to a command line, you can [Restrict Users](/docs/otter/configuring-for-your-team/otter-administration-security){target="_blank"} from viewing debug-level logs. This offers an ideal mix between security/compliance and visibility.

## Assets in Plans
When an [Asset](/docs/otter/scripting-in-otter/otter-core-concepts-assets){target="_blank"} (template plan or script) is used in a plan, it is run as an operation, and can be treated in the same way (moved within a plan and have custom configurations). It is logged as an operation as well

### More on this topic:
* [General Blocks](/docs/executionengine/overview/statements-and-blocks/otter-execution-engine-statements-and-blocks-general-blocks){target="_blank"}
* [Loop Blocks](/docs/executionengine/otterscript/statements-and-blocks/loop){target="_blank"}
* [Try / Catch Blocks](/docs/executionengine/otterscript/statements-and-blocks/try-catch){target="_blank"}
* [ If / Else Blocks](/docs/executionengine/overview/statements-and-blocks/otter-execution-engine-statements-and-blocks-if-else){target="_blank"}
* [Other Statements](/docs/executionengine/overview/statements-and-blocks/otter-execution-engine-statements-and-blocks-other-statements){target="_blank"}
