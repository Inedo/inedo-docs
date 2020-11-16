---
title: Overview
subtitle: OtterScript & Execution Engine
sequence: 100
keywords: buildmaster, otterscript
---

To deploy your applications and releases, BuildMaster uses an advanced execution engine that's capable of running thousands of different operations and scripts on thousands of different servers.

While BuildMaster's execution engine is an integral part of BuildMaster itself, the core engine was designed to be shared across products, and is also used in [Otter](/docs/otter) (our Infrastructure as Code tool) and [Romp](/docs/otter/reference/romp) (a stand-alone, command-line tool).

## Operations {#operations data-title="Operations"}

Operations are the tasks that the execution engine will run on a server. BuildMaster ships with dozens of [built-in operations](/docs/buildmaster/reference/operations) and you can add more through [BuildMaster Extensions](/docs/buildmaster/reference/extensions), to see a list of all of our extensions head over to the [Extensions feed](https://proget.inedo.com/feeds/Extensions).

Some operations are quite simple (Sleep), while others are quite complex (Synchronize Files across Servers). Through a combination of Operations, Scripts, Variables, and Blocks, you will be able to model any plan to deploy an application to an environment.

## OtterScript in BuildMaster {#otterscript data-title="OtterScript in BuildMaster"}

OtterScript is a Domain-Specific Language that was designed in tandem with the execution engine to represent [configuration plans](/docs/otter/core-concepts/plans#configuration) and [orchestration plans](/docs/otter/core-concepts/plans#orchestration) in Otter, and [deployment plans](/docs/buildmaster/ci-cd/deployments/plans) in BuildMaster.

You really don't need to learn OtterScript; it's simply the textual representation of a plan, and plans are already fully editable in the drag-and-drop plan editor. If you switch back-and-forth between visual and code modes, you'll probably learn it on your own, but it's also pretty well documented.

{.docs}
 - [OtterScript Overview](/docs/executionengine/otterscript/overview)
 - [Comments & Descriptions](/docs/executionengine/otterscript/comments-and-descriptions)
 - [Formal Grammar](/docs/executionengine/reference/formal-grammar)
 - [Formal Specification](/docs/executionengine/reference/formal-specification)
 - [Strings & Values](/docs/executionengine/otterscript/strings-and-literals)

## Legacy Execution Engine {#legacy data-title="Legacy Execution Engine"}

BuildMaster also includes a separate, side-by-side execution engine that is used to run deployment plans developed prior to v5. Although the legacy engine will be indefinitely supported, we don't recommend using it for new applications/plans.

The documentation in this section will not focus on the legacy engine, but [KB#1107- Importing BuildMaster v4 Deployment Plans](https://inedo.com/support/kb/1077/configuring-your-inedo-product-to-run-as-a-windows-domain-account) contains plenty of details on the differences, as well as migration strategies.
