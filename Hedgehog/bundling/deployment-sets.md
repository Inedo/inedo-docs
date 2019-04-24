---
title: Deployment Sets
sequence: 200
keywords: hedgehog, packages, deployment sets

---
Deployment sets are used to bundle a set of related packages - such as a PHP-based web frontend and a NodeJS-based API middle-tier- so that they can be delivered and deployed together


:::attention{.analogy}
![](/resources/images/icons/analogy.png) Think of a deployment set like an empty shipping pallet, with a manifest form attached that's filled out in pencil
:::


Not all of the packages in a set need to be deployed to the same targets at the same time. For example, your web frontend package might go to a different group of testing servers than your API middle-tier package. But the entire package is logically "delivered" to the same "testing stage".

Deployment sets can be simple, referencing just a single package from a package source. Or they can contain various packages from different package sources.

A deployment set contains its own metadata and variables, which can be used when deploying packages.

:::attention{.analogy}
![](/resources/images/icons/analogy.png) When shipping goods on pallets, it's generally best to add packages to your pallet, wrap it up, and then send it along its way. Of course, you can add or remove packages to the pallet en route, or change the manifest form at any time, but that makes things more complicated.
:::

Deployment sets are similar. Generally speaking, you should set-up your deployment set early on in the process, but you have the flexibility to modify the deployment set as needed, either by editing metadata, or adding/removing packages, or variables as needed

You can use a [deployment set template](/support/documentation/hedgehog/bundling/deployment-sets/templates) to greatly simplify the process of complex deployment set creation.

### Status and Lifecycle {#status data-title="Status and Lifecycle"}

A deployment set can be in one of three statuses that signify where the deployment set is in its lifecycle:

{.docs}
- **Active:** progressing through its pipeline with the possibility of being deployed to the final stage
- **Deployed:** successfully deployed to the final stage of its pipeline
- **Rejected:** not deployed to its final stage, and instead determined to be inadequate or otherwise inappropriate for the final stage


When a deployment set is successfully deployed to the final stage of its pipeline, the status is automatically changed to Deployed. Users may reject deployment sets and, depending on pipeline configuration, deployment sets may automatically be rejected if another deployment set enters the same stage.

Administrators may also change the status of a deployment set at any time. This should only be done in exceptional situations, such as to append additional metadata to deployed packages, to "unreject" a deployment set, or correct other mistakes.

### Variables {#variables data-title="Variables"}

Deployment sets have a set of [configuration variables](/support/documentation/hedgehog/deliver-deploy/configuration-variables) associated with them that can be used by deployment plans at runtime.

### Properties and Metadata {#metadata data-title="Properties and Metadata"}

Deployment sets have the following metadata properties; they may be accessed from within a deployment plan by using variable functions, and can be changed from the UI or API.

| <!-- -->    | <!-- -->    |
| ----        | ----        |
| Id          | this is an immutable system-assigned identifier, and is used both internally and by the API to reference a specific deployment set; it cannot be changed and is unique across all deployment sets in an instance of Hedgehog |
| Name:       | this is a human-friendly name used within the UI to help differentiate one deployment set from another; it is not unique across deployment sets |
| Template:  | the name of the template used to create the deployment set; this is also used to determine deployment-time variables |
| Pipeline:  | this references the pipeline that is used to deploy the deployment set through a sequence of stages |
| Project or Release: | a deployment set must be associated with either a [project](/support/documentation/hedgehog/bundling/projects) or [release](/support/documentation/hedgehog/bundling/releases) you can change this on active deployment sets

You can use *raft item syntax* for Template and Pipeline names.

:::attention{.technical}
**Warning:** changing a deployment set's pipeline after that package has been deployed to stages within another pipeline may yield unpredictable behavior and inaccurate metadata.
