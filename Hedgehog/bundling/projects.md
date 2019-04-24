---
title: Projects
subtitle: Projects in Hedgehog
sequence: 400
keywords: hedgehog, packages, projects

---
Projects are used to organize and visualize your deployment sets, pipelines, releases, templates, and plans. You can also use them for security access control by granting and denying tasks to different users across projects. They can also be nested, which means you can model complex applications or organizational units as needed.

## Working with Projects & Sub-projects {#working data-title="Working with Projects & Sub-projects"}

:::attention{.analogy}
![](/resources/images/icons/analogy.png) Think of a project like a file system folder/directory, and the project's contents (pipelines, releases, templates, etc.) like the files within that folder. Just like folders, projects can be nested and their contents can reference other projects' contents, such as having a pipeline in one project reference a plan in another project.
:::

### Variables {#variables data-title="Variables"}

[Configuration variables](/support/documentation/hedgehog/deliver-deploy/configuration-variables) may be scoped to a project, and can be seen and created on the project's settings page. These variables can be used by anything within the project: pipelines, plan, and even sub-projects.

### Properties and Metadata {#metadata data-title="Properties and Metadata"}

Projects have the following metadata properties; they may be accessed from within a deployment plan by using [variable functions](/support/documentation/executionengine/components/runtime-variables#variable-functions).

{.docs}
- **ProjectId:** this is an immutable system-assigned identifier, and is used both internally and by the API to reference a specific project; it cannot be changed and is unique across all projects in an instance of Hedgehog
- **Name:** this is a human-friendly name used within the UI to help differentiate one project from another; it is unique to its parent project (or the global scope, if no parent project)
- **Description:** a markdown-formatted field that can contain any user-defined text; it is displayed on the project's home page
- **ParentProjectId:** an optional reference to a parent project; this must be set to another project that would not cause a circular reference
- **Raft Name:** the [raft](/support/documentation/hedgehog/global-components/rafts) that the project will use for its contents; this is an advanced option, and won't be available to change until you've created multiple rafts
- **Active / Inactive:** you can deactivate a project, which will mostly hide it from the user interface and in most API calls. Unless you explicitly purge a project, a project will be deactivated
