---
title: Best Practices
subtitle: Projects; Best Practices
sequence: 100
keywords: hedgehog, packages, projects
show-headings-in-nav: true

---
:::attention {.best-practice}
![](/resources/images/icons/best-practices.png) Like folders, projects require a unique name within their containing project. Aside from that, there are no restrictions as to what kinds of contents a project can contain.
:::

You don't want to end up with a project hierarchy that mimics that nightmare share drive, with a mess of random files and folders scattered throughout.

### Avoid Simple, Context-free Names {#simple data-title="Avoid Simple, Context-free Names"}

From a UI perspective, it's not feasible to always display a project's full path. Operating systems have the same challenges with folder hierarchies. As such, you should avoid creating a project hierarchy that relies on short project names, or that rely entirely on the parent projects to understand context. For example, the name "Rel" is meaningless without the hierarchy like "HDARS > Current > Rel". Instead, HdarsRel might be better.

### Avoid Relying on Cascading Behavior {#cascading data-title="Avoid Relying on Cascading Behavior"}

As the [anti-example](/support/documentation/hedgehog/bundling/projects/content-name#anti-example) demonstrates, content name resolution allows for a "cascading" behavior of sorts. You can have a child project "override" a pipeline or plan in the parent project simply by creating an item with the same name.

This is a generally bad idea, because it will be hard for other users – and yourself, down the line – to distinguish when something is "overridden" or when it's following a "standard". This isn't something we will try to "solve" in the UI, because this feature simply isn't intended to be used for this purpose.

If you need to make an exception to an "inherited" behavior, then you should use a different plan name. For example, if one of your dozen libraries behaves differently than all the others, then you should use differently-named templates, plans, or pipelines. Or use variables to control the behavior. Just don't hide it in a "cascaded" item.

### Be Careful When Renaming and Modifying Hierarchy {#renaming data-title="Be Careful When Remaining and Modifying Hierarchy"}

Because content can be cross-referenced across projects, you should be careful when changing a project's name or parent project. If any content is using a [*fully-qualified syntax*](/support/sdk-reference/inedosdk/Inedo.Extensibility.RaftRepositories) to reference an item within a project you rename or "move", then that resolution may fail.

Even though Hedgehog will detect and fix references when you move or rename a project, there are many cases when it's simply not possible. For example, if you use a variable to determine the name of a plan to use in a pipeline, there's no way for Hedgehog to know how that variable gets set, or that it might get set to the "old" name of a content item.
