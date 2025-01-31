---
title: "What is a Package in ProGet?"
order: 1
---


A **package** is a file that contains other files.

Unlike packages on NuGet.org or other open-source sites (RubyGems, Chocolatey, etc.), ProGet packages are private to you, even if they come from a third-party source.

The manifest file included in the package contains the package name and version. It tells you who did what to the package and when, which simplifies two of the most stressful development events: auditing and rollbacks. This is because packages can be:

- [Repackaged](/docs/proget/packages/repackaging) to indicate that a package has been tested
- [Promoted](/docs/proget/packages/repackaging) to indicate a change in quality or who may access it
- [Blocked](/docs/proget/sca/licenses) from ever entering ProGet (and your environments)
- [Inspected](/docs/proget/sca/vulnerabilities) for vulnerabilities using quality controls that you define


:::(Info)
Packages help you distribute your applications and components uniformly. They have become a unifying concept across DevOps toolchains because packages are built once and deployed consistently across environments. This way, you can be sure that what is deployed to production is *exactly* what has been tested.
:::


Because ProGet is a **self-managed** package repository, every package you store in ProGet remains as private to your organization as you want it to be. And unlike simple solutions like nuget.server, ProGet can support many hundreds of packages in a single instance, even in the free-forever version. These packages can be cached copies of third-party open source packages or proprietary packages created by your team, and everything in between.

With ProGet, you get much more than just a place to store packages:

- [Replication](/docs/proget/replication-feed-mirroring/proget-advanced-feed-replication)
- [Retention](/docs/proget/administration/retention-rules)
- [Connectors](/docs/proget/feeds/connector-overview)
- [Policies and Compliance Rules](/docs/proget/sca/policies)
- [Vulnerability Scanning](/docs/proget/sca/vulnerabilities)
- [License Detection](/docs/proget/sca/licenses)
- [Cloud Storage](/docs/proget/advanced-features/proget-advanced-cloud-storage)
- And much more

In the rest of this page, you will learn more about what packages are, how to use them, and common questions. Looking for a specific package type? You can find the documentation for the different package types in the menu on the left side under "Feeds Types & Third-Party Packages"

## What's Inside a Package?

A package consists of a .zip file that contains the files you want to distribute and a manifest file that describes the package itself (for example, who processed the package and when). The specific layout of the .zip file and the manifest is called the package format.

## How is a Package Different from an Artifact?

Artifacts can be all kinds of files (e.g., .jar, .war, .dll, .rpm, .zip, .jpg). Artifacts are just files; no manifest data is included. You may have the file you need, but not the context you may need.

Packages have a format defined by standards (e.g. NuGet, PyPi, etc.) and contain not only the files, but also the metadata.

## What Package Types Does ProGet Support?

In addition to the [Universal Package](/docs/proget/feeds/universal) type, ProGet supports a growing number of package types:

- [NuGet](/docs/proget/feeds/nuget)
- [PowerShell](/docs/proget/feeds/powershell)
- [Ruby Gems](/docs/proget/feeds/rubygems)
- [Visual Studio Extension (.vsix)](/docs/proget/feeds/vsix)
- [Maven (Java)](/docs/proget/feeds/maven)
- [npm (Node.js)](/docs/proget/feeds/npm)
- [Chocolatey (Windows/Machine)](/docs/proget/feeds/chocolatey)
- [Debian (apt)](/docs/proget/feeds/debian)
- [Helm (Kubernetes)](/docs/proget/feeds/helm)
- [PyPi (Python)](/docs/proget/feeds/pypi)
- [RPM (yum)](/docs/proget/feeds/rpm)
- [Docker (containers)](/docs/proget/docker/private-registries)

Not seeing the package type you want? We are always looking for input on [other package and feed types](/docs/proget/feeds/other-types) to support.

## Using Third-party/Open-source Packages

In many cases, you'll rely on third-party, open-source packages for your development, such as NuGet or Chocolatey. To use these packages in ProGet, first create a [feed](/docs/proget/feeds/feed-overview) for that package type. Then add a [connector](/docs/proget/feeds/connector-overview) to your ProGet feed and point ProGet to the correct URL. The feed is then populated with packages from the external source.

You can avoid wasting time waiting for third-party packages to download by pulling a local copy of the package into ProGet. You can also [cache packages and/or metadata](/docs/proget/feeds/connector-overview#connector-caching) to ensure that the packages you need are available in ProGet when you need them.

## Creating and Publishing Proprietary Packages 

Many third-party package formats, such as NuGet, can be built internally for your own use. Using a tool like BuildMaster, you can [apply CI/CD's rigorous tests to the creation of .nupkg packages](/docs/buildmaster/development-platforms/buildmaster-platforms-dotnet/buildmaster-platforms-dot-net-nuget) that you make for private use. See out the detailed documentation for [more popular package types](/docs/proget/packages/what-is-a-package#supported) for instructions on creating proprietary packages.

After you create a package, there are four ways to publish it to ProGet for use:

- **Upload from disk**: uploads a prepackaged package from disk
- **Push with client tool**: uses the command line tool, such as nuget.exe or npm.exe, to add a package to the local feed
- **Pull from another feed**: pulls a package from another feed of the same package type
- **Bulk package import/file copy**: imports multiple existing packages at once

For more detailed instructions on how to add a package to a feed while *in ProGet*, click the 'Add Package' button in the ProGet feed for that package type.

If there is a third-party package format designed for your particular case, we recommend that you use it. However, in many cases, [universal packages](/docs/proget/feeds/universal) provide the best combination of simplicity, utility, and extensibility.

## Identifying and Versioning Packages 

One of the most important aspects of a package is that it is uniquely identifiable by a name and version. This simple, human-readable identification makes it so easy to distribute and use packages.

For example, "HDars-API 1.0.4" is version 1.0.4 of HDars-API, which is newer than "HDars-API 1.0.2," older than "HDars-API 1.3.0," and different than "HDars-Web 1.0.4." "HDars-API" by itself is fairly meaningless, because it could refer to any version of HDars-API.

Universal packages (as well as some third-party packages) use the [SemVer specification](https://semver.org/) to describe the version number.

## Common Package Operations 

### Publish/Upload

To publish or upload a package to ProGet, follow these steps within ProGet:
1.	Create a feed for this package type, or open the feed you want to upload the file to. 
2. Click the ▼ to the right of the "Manage Feed" button. In the drop-down list, you will see "Add Package."
3.	In the pop-up window, select the upload method you want to use.

### Delete/Remove

To delete or remove a package in ProGet, perform the following steps in ProGet:
1.	Go to the Packages tab on the top ribbon.
2.	Click the red X on the right side of the package you want to delete.
3.	In the pop-up window, select "Yes, delete package." This is a permanent action. 
*Note:* You can optionally delete all versions of the package, which will delete all versions of that package within that feed only. To delete a package that has been moved between feeds, repeat these steps.

### Unlist

"Unlist" reduces visibility in searched. Packages won't show up unless you enter the exact URL. 

*Note: This is only supported in [NuGet](/docs/proget/feeds/nuget#deleting-and-unlisting)

### Promote

[Package promotion](/docs/proget/packages/package-promotion) in ProGet lets you separate packages by quality. Promoting a package leaves the package in the source feed and duplicates it in the target feed. You can manually delete the "older" package later, or configure the retention rules to do this for you.

To promote a package to ProGet, follow these steps inside ProGet:

1.	Go to the package overview page for the package you wish to promote. 
2.	Hover over the ▼ by "Download Package," select "Promote Package" from the dropdown.
3.	Select the target feed from the dropdown.

See the [Package promotion documentation](/docs/proget/packages/package-promotion) for more details.