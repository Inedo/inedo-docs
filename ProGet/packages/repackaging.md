---
title: Prerelease Packages & Repackaging
subtitle: Prerelease Packages & Repackaging
keywords: packaging, repackaging, upack, nuget, universal-packages, proget
sequence: 200
show-subheadings-in-nav: true
---

::: {.attention .best-practice}
Repackaging is available in paid and trial ProGet editions starting in v5.2.5
:::

Through prerelease versioning, it's clear to everyone which packages are release-quality and what is not ready for release. Like all software, it is important to create several prerelease packages that are tested and validated prior to creating a final, stable package. You can use different prerelease versions for this purpose, for example:

{.docs}
- `ProfitCalc 3.2.4-ci.54` may signify a package created on CI server (build # 54)
- `ProfitCalc 3.2.4-beta.2` may signify a second beta version that customers may test
- `ProfitCalc 3.2.4-rc.1` may signify a package is ready for final release, pending some last-minute testing
- `ProfitCalc 3.2.4` is the production, release-quality package

This versioning methodology is something that both the business and development can understand.


## From Prerelease to Stable {#workflow data-title="Package Workflow"}

There are some basic rules regarding packaging and software development that our industry tends to follow closely.

1. Packages are immutable. Once published, a package file should never be modified. This means you can't "edit" a version number of a package because the version number is part of the metadata embedded in the file.
2. Software should never be deployed to a production environment without being tested thoroughly.
3. Rebuilding source code can produce different software, and software with wildcard version dependencies can introduce *dependency hell* simply by rebuilding.
4. A prerelease package should never be released.

These rules are discussed at length in our book [Continuously Scale and Continuously Deliver with a Universal Package Manager](https://inedo.com/support/resources/ebooks/continuously-scale-deliver-upm).

Given these rules, it seems nearly impossible to create a logical pipeline that both the business and development can understand and follow.

This is where repackaging comes in. Repackaging is the process of creating a new package from an existing package using exactly the same content as the original package. While the metadata of the package changes, a history of the package names and versions is also recorded for audit purposes.

## How To Repackage a Package {#repackage data-title="How to Repackage"}

Repackaging is a built-in feature for all Universal and NuGet packages and can be done with a click of a button or with a [simple API call](/docs/proget/reference/api/repackaging).

To use the ProGet web interface:

1. Browse to any Universal or NuGet package in ProGet
2. Click on the the "Repackage" button found under _Local Operations_
3. Enter a new version number, and you may also optionally add a reason (for auditing purposes)
4. Click "Repackage"

## Viewing and Auditing Repackage History {#history data-title="Repackage History"}

ProGet allows users to see the repackaging audit trail on the "History" tab of the package. The history will show key values regarding each repackaging event such as the date, time, version, user name, notes, and more. If the orginal package resides in ProGet you can access it from this page.

These historic audit trails are embedded within the package itself and allows you to easily replicate or promote this package to other feeds while preserving the repackaging history.

For Universal packages, the history is embedded in `upack.json`. For NuGet packages, the history is embedded (in the same JSON format) in `.progetRepackagingHistory.json` in the `.nupkg` file.

## Other Repackaging Options {#options data-title="Other Repackaging Options"}

Aside from using the ProGet UI we offer two other options for repackaging a package. You can either make simple [API calls](/docs/proget/reference/api/repackaging) or use the [Universal Package CLI](/docs/upack/tools-and-libraries/upack-cli) for Universal packages. These options give you a lot of flexibility when it comes to automating your repackaging process.

### Repackaging in a CI/CD Pipeline {#ci-cd data-title="Repackaging in a CI/CD Pipeline"}

We have built an example application on our [public BuildMaster instance](https://buildmaster.inedo.com) that uses the [repackaging API](/docs/proget/reference/api/repackaging) to demonstrate the patterns that can be used to add repackaging as part of a CI/CD pipeline. The application contains deployment plans, pipelines, and source control repository monitor configuration such that commits pushed to any source control repository branch will automatically create a package and deploy it to a CI repository. Deploying the build through future pipeline stages causes it to be repackaged and then redeploys it to a [public repository](https://proget.inedo.com/feeds/ExternalBuild) used to build Inedo projects that require NuGet packages.

You can view the example application here: [Inedo.Core Library](https://buildmaster.inedo.com/applications/44/)
