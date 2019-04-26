---
title: Execution Engine
subtitle: Execution Engine
sequence: 400
keywords: hedgehog, execution-engine

---
To deploy your applications and releases, Hedgehog uses an advanced execution engine that's capable of running thousands of different operations and scripts on thousands of different servers.

While Hedgehog's execution engine is an integral part of Hedgehog itself, the core engine was designed to be shared across products, and is also used in [Otter](/otter) (our Infrastructure as Code tool) and [Romp](/romp) (a stand-alone, command-line tool).

## OtterScript in Hedgehog {#otterscript data-title="OtterScript in Hedgehog"}

[OtterScript](/support/documentation/executionengine/otterscript/overview) is a Domain-Specific Language that was designed in tandem with the execution engine to represent [configuration plans](/support/documentation/otter/core-concepts/plans#configuration) and [orchestration plans](/support/documentation/otter/core-concepts/plans#orchestration) in Otter, and [deployment plans](/support/documentation/hedgehog/deliver-deploy/deployment-plans) in Hedgehog.

You really don't need to learn OtterScript; it's simply the textual representation of a plan, and plans are already fully editable in the   drag-and-drop plan editor. If you switch back-and-forth between visual and code modes, you'll probably learn it on your own, but it's also pretty well documented.

{.docs}
- [OtterScript Overview](/support/documentation/executionengine/otterscript/overview)
    - [Comments & Descriptions](/support/documentation/executionengine/otterscript/comments-and-descriptions)
    - [Formal Grammar](/support/documentation/executionengine/reference/formal-grammar)
    - [Formal Specification](/support/documentation/executionengine/reference/formal-specification)
    - [Strings & Values](/support/documentation/executionengine/otterscript/strings-and-literals)
