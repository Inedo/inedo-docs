---
title: Content Name Resolution
subtitle: Content Name Resolution
sequence: 200
keywords: hedgehog, packages, projects

---

## Project Content Name Resolution {#resolution data-title="Project Content Name Resolution"}

Most content items reference things by name. For example, a pipeline named "Hotfix" might reference a plan named "Emergency Deploy" in a stage named "Production". While you can use the [*fully-qualified syntax*](/support/sdk-reference/inedosdk/Inedo.Extensibility.RaftRepositories), it's best to let Hedgehog resolve simple names for you, especially when content items are contained within the same project.

When searching for content with an unqualified name, Hedgehog will first look in the current project for content with that name and type. If no content is found, then the parent project is searched, and then the ancestors, all the way to the global (i.e. no project) level.

### Example: Sharing Resources with a Hierarchy {#hierarchy data-title="Example: Sharing Resources with a Hierarchy"}

One common use case for project nesting is to use a parent project as a sort of "container" for both other projects, and the content that those projects will use.

:::attention{.best-practice}
![](/resources/images/icons/best-practices.png) For example, consider a set of libraries that are all published in a consistent manner, using the same template ("Library"), which references a pipeline ("Standard") and a deployment plan ("Publish Library"):
:::

```
/projects
      /CommonLibraries
            /templates
                Library.json
            /plans
                Publish-Library.otter
            /pipelines
                Standard.json
            /projects
                  /Library1
                  /Library2
                  /Library3
```

With this layout, new library projects can easily be added by simply creating a project under the "CommonLibraries" project. All package sets in these libraries can use the same pipeline and plan. So long as the same "Library" template is used, everything will be built and deployed consistently. Of course, the packages or projects themselves can have variables to control how and where they are published.

### Anti-example: Confusing Hierarchy {#anti data-title="Anti-example: Confusing Hierarchy"}

:::attention {.technical}
This following layout is *pretty bad*, and you should never set anything up like this, because it's quite confusing.
:::

But it demonstrates how the name resolution works:

```
/projects
      /ParentProject
            /plans
                Emergency-Deploy.otter
            /pipelines
                Hotfix.json
            /projects
                  /ChildProject1
                        /plans
                            Emergency-Deploy.otter
              /ChildProject2
                     /pipelines
                        Emergency.json
```

When a package set in ChildProject1 specifies "Hotfix" as a pipeline, then ParentProject's Hotfix.json will be used. If ParentProject's HotFix specifies "Emergency Deploy" as a plan, then that package set will use ChildProject1's Emergency-Deploy.json, and *not* the ParentProject's version.

When a package set in ChildProject2 specifies "Emergency" as pipeline, then ChildProject2's Emergency.json will be used. If this specifies "Emergency Deploy" as a plan, then the package set will use ParentProject's Emergency-Deploy.otter.
