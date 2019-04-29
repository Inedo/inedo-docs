---
title: Releases
subtitle: Releases in Hedgehog
sequence: 200
keywords: hedgehog, packages, releases

---
A release is an event where a planned set of changes are tested and delivered to production (or another final stage) using a set of packages. They help you organize and visualizing related deployment sets with a set of [configuration variables](/support/documentation/hedgehog/deliver-deploy/configuration-variables) that all deployment sets can access, target dates that can assist with release planning, and notes that can facilitate communication.

Release and deployment sets are conceptually similar, but it’s at a higher abstraction level. Just as a deployment set "contains" packages, a release will "contain" deployment sets.

![](/resources/documentation/hedgehog/abstraction-level.png)

## Working with Releases {#working data-title="Working with Releases"}

:::attention{.analogy}
![](/resources/images/icons/analogy.png) Think of release like a rough plan that guides your team, in delivering a required set of changes through testing, and on to its final destination.
:::

You'll probably have a desired production delivery date, as well as other key dates like when internal and client testers will schedule time to review the changes. You may also know, in advance, which pipeline and deployment sets template the release should use.

While releases primarily serve as a visualization, they can also help define and facilitate better self-service processes. For example, a team lead may create multiple releases at once – one planned for next week, another for next month, and an emergency hotfix – and different engineers will be able to deliver different changes of the same application to different testers, all without stepping on each other's toes.

### Status and Lifecycle {#status data-title="Status and Lifecycle"}

A release can be in one of three statuses that reflect the state of development:

{.docs}
- **Active:** deployment sets are being created and deployed through pipelines
- **Deployed:** a deployment set was deployed to the final stage of its pipeline
- **Canceled:** no deployment set was deployed to the final stage, and deployment sets are no longer being created or deployed; all deployment sets are also rejected

When a deployment set is successfully deployed to the final stage of its pipeline, the release status is automatically changed to Deployed. This is controlled by the pipeline, and can be configured to behave differently.

Administrators may also change the status of a release at any time. This should only be done in exceptional situations, such as to append additional metadata to a release, to "unreject" a deployment set, or correct other mistakes.

### Variables {#variables data-title="Variables"}

Releases have a set of [configuration variables](/support/documentation/hedgehog/deliver-deploy/configuration-variables) associated with them that can be used by deployment plans at runtime.

### Properties and Metadata {#metadata data-title="Properties and Metadata"}

Releases have the following metadata properties; they may be accessed from within a deployment plan by using [variable functions](/support/documentation/executionengine/components/runtime-variables#variable-functions) and can be changed from the UI or API.

{.docs}
- **Id:** this is an immutable system-assigned identifier, and is used both internally and by the API to reference a release; it cannot be changed and is unique across all releases in an instance of Hedgehog
- **Number:** this is a human-friendly name in a "SemVer2" format; it is unique across releases in a project
- **Name:** this is a human-friendly name used within the UI to help differentiate one release from another; it is not unique across releases
- **Template:** the name of the [deployment set template](/support/documentation/hedgehog/bundling/deployment-sets/templates) used to create deployment sets; this is also used to determine deployment-time variables
- **Project:** a release must be associated with a [project](/support/documentation/hedgehog/bundling/projects); you can change this on active releases

You can use [raft item syntax](/support/sdk-reference/inedosdk/Inedo.Extensibility.RaftRepositories/RaftItem) for Template names. 
