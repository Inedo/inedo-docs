---
title: Prerelease Packages & Repackaging
subtitle: Prerelease Packages & Repackaging
keywords: packaging, repackaging, upack, nuget, universal-packages, proget
sequence: 200
show-subheadings-in-nav: true
---

Prerelease versioning is a package-naming best practice. Adding contextual information directly into the package name indicates package quality and prevents production-unready packages from being used in production. [Repackaging](#repackage) -ci or -rc prerelease packages in ProGet ensures that only tested and validated packages move forward for production use.

*Example prerelease and stable package versions:*

{.docs}
- `ProfitCalc 3.2.4-ci.54` may signify a package created on CI server (build # 54)
- `ProfitCalc 3.2.4-beta.2` may signify a second beta version that customers may test
- `ProfitCalc 3.2.4-rc.1` may signify a package is ready for final release, pending some last-minute testing
- `ProfitCalc 3.2.4` is the "stable" or release-quality package

This versioning methodology is part of Semantic Versioning ([SemVer2](https://semver.org/#spec-item-9)). Among other benefits, SemVer naming is something that both the business and development can understand.


## From Prerelease to Stable {#workflow data-title="Package Workflow"}

There are some basic rules for packaging and software development that our industry tends to follow closely:

1. **Packages are immutable.** Once published, a package file should never be modified. This means you can't "edit" a version number of a package, because the version number is part of the metadata embedded in the file.
2. **Software should never be deployed to a production environment without being tested thoroughly.** This rule speaks for itself.
3. Rebuilding source code can produce different software, and **software with wildcard version dependencies can introduce *dependency hell* simply by rebuilding.**
4. **A prerelease package should never be released.** If it's prerelease, it is necessarily unready for production.

These rules and "dependency hell" are discussed at length in Chapter 3 of our book [Continuously Scale and Continuously Deliver with a Universal Package Manager](https://inedo.com/support/resources/ebooks/continuously-scale-deliver-upm). 

Given these rules, it seems nearly impossible to create a logical pipeline that both the business and development can understand and follow.

Repackaging lets you create a logical pipeline that follows these best practices *and* is something everyone can understand and follow. **Repackaging is the process of creating a new package from an existing package using exactly the same content as the original package. While the metadata of the package changes, a history of the package names and versions is also recorded for audit purposes.**

## ProGet Free Edition Limitations {#free-limitations data-title="Limitations in Free"}

Repackaging is limited in ProGet Free. You can repackage, but only through the web interface (not through the API). 

Additionally, you won't be able to see who repackaged or when. On the "Package Overview" page's "History" tab, repackaging and promotion history will display as `date hidden` and `username hidden` in the log.

## How To Repackage a Package in ProGet {#repackage data-title="Repackaging in ProGet"}

Repackaging is a built-in feature for all Universal and NuGet packages. You can repackage using [simple API call](/docs/proget/reference/api/repackaging) or directly in the ProGet web interface.

To repackage via the ProGet web interface:

1. Go to the "Package Overview" page for any Universal or NuGet package in ProGet.
2. Select "Repackage" from the dropdown.
3. Enter a new version number. You may also optionally add a reason (for auditing purposes).
  * Selecting the same feed from the dropdown just repackages the package, leaving it in the same feed.
  * Selecting a *different* feed from the dropdown promotes the repackaged package to the target feed (the repackaged version *will not* show up in the source feed).
4. Click "Repackage."

## Viewing and Auditing Repackage History {#history data-title="Repackage History"}

ProGet allows users to see the repackaging audit trail on the "History" tab of the package. The history will show key values regarding each repackaging event such as the date, time, version, username, notes, and more. 

These historic audit trails are embedded within the package itself and allows you to easily replicate or promote this package to other feeds while preserving the repackaging history. For Universal packages, the history is embedded in `upack.json`. For NuGet packages, the history is embedded (in the same JSON format) in `.progetRepackagingHistory.json` in the `.nupkg` file.

Note: [This is limited in ProGet Free](#free-limitations).

## Other Repackaging Options {#options data-title="Other Repackaging Options"}

Aside from using the ProGet UI, we offer two other options for repackaging a package. You can either make simple [API calls](/docs/proget/reference/api/repackaging) or use the [Universal Package CLI](/docs/upack/tools-and-libraries/upack-cli) for Universal Packages. These options give you a lot of flexibility when it comes to automating your repackaging process.

You can also repackage and [promote](/docs/proget/packages/package-promotion/) in one step by selecting a different target feed from the dropdown when repackaging.

### Repackaging in a CI/CD Pipeline {#ci-cd data-title="Repackaging in a CI/CD Pipeline"}

We have built an example application on our [public BuildMaster instance](https://buildmaster.inedo.com) that uses the [repackaging API](/docs/proget/reference/api/repackaging). This example demonstrates the patterns that can be used to add repackaging as part of a CI/CD pipeline.

The application contains deployment plans, pipelines, and source control repository monitor configuration such that commits pushed to any source control repository branch will automatically create a package and deploy it to a CI repository. Deploying the build through future pipeline stages causes it to be repackaged and then redeploys it to a [public repository](https://proget.inedo.com/feeds/ExternalBuild) used to build Inedo projects that require NuGet packages.

You can view the example application here: [Inedo.Core Library](https://buildmaster.inedo.com/applications/44/)
