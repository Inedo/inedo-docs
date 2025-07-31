---
title: "Deployables"
order: 3
url-slug: "buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-applications-concepts-deployables"
---

:::(Error) (Deployables were Removed in BuildMaster 2025)
With the introduction of OtterScript and the Inedo Execution Engine in BuildMaster v5, this feature was redundant and eventually removed in BuildMaster 2025. See [Legacy (Deprecated) Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy) to learn more.
:::

A deployable is meant to represent a deployable component of an application. For example, if you have a multi-tier application called HDARS, it might have an HDARS web, an HDARS service, and an HDARS-API deployable to represent each of the corresponding tiers.

## Adding Deployables
:::(Info)
As of BuildMaster 6.1.20, deployables are hidden in the UI unless the advanced setting `Legacy.Deployables` is enabled.
:::
You can add deployables to your application from the Application Settings tab. Once you create a deployable for your application, you can select it for use in specific contexts, such as:

- In the Visual Plan Editor, you have the option to specify a deployable name
- When creating a release, you choose which deployables to include
- You can specify a deployable for database connections and change scripts
- You can filter and search for releases based on the deployables they contain

When adding an imported deployable from another application, select a specific release of the other application when creating a release.

## Deployables and OtterScript 

In deployment plans, a deployable is used in a general block as follows:

```
for deployable HDARS-Web
{
    ... operations ...
}
```

This sets the current deployable in context to HDARS-Web, which means that the block will be skipped if HDARS-Web is not included in the release.

You can also add variable key/value pairs to a deployable. When a deployable is in context (as in the case above), this variable is evaluated.

If HDARS-Web was an imported deployable, $ReleaseNumber would show the number of this application instead of the current version number.

## Migrating Away from Deployables

Deployables should be replaced with variables. For example:

```
if $DeployHdarsWeb
{
    ... operations ...
}
```

You can prompt for `$DeployHdarsWeb` as a checkbox at Release or Build creation time.