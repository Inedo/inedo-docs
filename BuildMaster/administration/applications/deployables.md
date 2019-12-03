---
title: Deployables
subtitle: Deployables
keywords: buildmaster, applications, deployables
sequence: 700
show-headings-in-nav: true
---

:::attention {.technical}
Deployables are an advanced feature, and with the introduction of OtterScript and the Inedo Execution Engine in BuildMaster v5, they offer significantly less benefits than in previous versions.
:::

:::attention {.technical}
Starting with BuildMaster v6.2.20, deployables are not visible in most of BuildMaster unless the advanced setting `Legacy.Deployables` is enabled.
:::

A deployable is intended to represent a "deployable component" of an application. For example, if you had a multi-tier application called HDARS, it might have an HDARS-Web, HDARS-Service, and HDARS-API deployable to represent each of the corresponding tiers.

You can add deployables to your application from the application settings tab, and once you create a deployable for your application, you will be able to select it for use in some contexts, including:

{.docs}
- In the Visual Plan Editor, you will be given the option to specify a deployable name
- When creating a release, you will select which deployables are to be included
- You can specify a deployable for database connections and change scripts
- You can filter and search for releases based on the deployables they include

If you add an "imported deployable" from another application, you will select a specific release of the other application when creating a release.

### Deployables and OtterScript {#deployables-and-otterscript data-title="Deployables and OtterScript"}

In deployment plans, a deployable is used in a general block as follows:

```
for deployable HDARS-Web
{
    ... operations ...
}
```

This will set the "current deployable in context" to HDARS-Web, which means that if HDARS-Web is not included in the release, then the block is skipped.

You can also add variable key/value pairs to a deployable. When a deployable in context (as the case above), that variable will be evaluated.

If HDARS-Web was an "imported deployable", then $ReleaseNumber would resolve to that application's number instead of the current release number.

:::attention {.best-practice}
![](/resources/images/icons/best-practices.png)

**Best Practices:** While we don’t consider them a legacy feature, they are mostly a "vestigial" feature that allowed previous versions of BuildMaster to perform "partial deployments" and cross-application orchestration (dependencies, artifacts, etc.).

However, you can also accomplish those goals through the use of configuration variables, release templates, and the "source" tab of the artifact operations. This may be more- or less-intuitive, depending on your familiarity with BuildMaster.

For monolithic applications, deployables are still quite useful, in that they are easier to visualize and work with than variables.
:::
