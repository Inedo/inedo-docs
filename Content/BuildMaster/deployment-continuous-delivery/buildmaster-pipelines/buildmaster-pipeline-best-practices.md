---
title: "Best Practices for BuildMaster Pipelines"
order: 1
---

BuildMaster's pipelines offer a lot of flexibility. In this article, we'll walk through a few best practices to consider when creating pipelines.

## 1. Start Simple
This level of flexibility can make it feel overwhelming to design the right pipeline. You can always add more to it later and adapt your deployment process as you learn how to improve it.

## 2. Keep Pipelines Reusable
Pipelines are designed to be used for all the deployment sets in a project and they represent a repeatable process. Don't treat them like a release checklist for a single release, and then start again on the next release.

## 3. Use Global Pipelines Sparingly
Global pipelines seem tempting at first: after all, a lot of your applications have a nearly identical workflow and having to define the same pipeline in each application seems wasteful. So why not just create one master pipeline that can be used by everything?

You could, but there are a few catches:
* **Inherently more complex**; you'll need to use global scripts, variables, and naming conventions to make global pipelines work across multiple applications
* **Accidental Usage**; it may not be very clear to users which pipeline to select, and they may pick the wrong one
* **Harder to change**; making just a single change will impact all the applications that use it, and that means you'll need to be extra careful to not break everything else

## 4.  Balance Stages vs. Targets vs. Scripts
You could, in theory, have a single deployment script that deploys a build from one server to another, all the way through production. On the other hand, you could have a pipeline with a thousand stages, each running a micro-step in deploying to a particular environment, not to mention targets.

To find the best middle ground, consider the design intent of each of the elements:
*   Stages are designed to visualize deployment sets as they make their way to production. Too many stages, and it becomes cluttered and almost useless. Come up with a sequence of stages that shows how others may visualize this progress over the course of days and weeks.
*   Targets are used to help keep your deployment plans reusable, and let you define servers and roles outside of the deployment plan. They are also visualized as a separate execution which may make identifying which parts of the deployment were successful, warned, or failed.
*   Scripts have a lot of flexibility, but they should generally be reusable and not server- or role-specific; but sometimes they are, and that's ok too.

## 5. Don't Abuse Pipeline Event Listener
Because you can run an OtterScript with a Pipeline Event Listener, you can theoretically do anything you want. But in general, these steps should be reserved for:

*   Auditing purposes
*   Sending notifications
*   Setting statuses
*   Tagging in Git