---
title: What is a "Package" in ProGet?
subtitle: Packages in ProGet
sequence: 100
keywords: proget, packages
show-headings-in-nav: true
---
A **package** is a file that contains other files.

Unlike packages on NuGet.org or other open-source sites (RubyGems, Chocolatey, etc.), ProGet packages are private to you, even if they originated from a third-party source.

The manifest data included in the package format gives you context on who did what and when, which simplifies two of the most stressful dev events: auditing and rollbacks. This is because packages can be:

{.docs}

- [Repackaged](/docs/proget/packages/repackaging) to indicate that a package has been tested
- [Promoted](/docs/proget/packages/package-promotion) to indicate a change in quality or who may access it
- [Blocked](/docs/proget/compliance/license-detection) from ever entering ProGet (and your environments)
- [Inspected](/docs/proget/compliance/vulnerabilities) for vulnerabilities using quality controls you define


**Packages help you uniformly distribute your applications and components. They have become a unifying concept across DevOps toolchains because packages are built once and deployed consistently across environments. Because of this, you can be certain that what does to production is *exactly* what was tested.** 

Because ProGet is a **self-managed** package repository, every package you store in ProGet stays as private to your organization as you like. And unlike basic solutions like nuget.server, ProGet can support many hundreds of packages in a single instance, even in the free forever version. These packages can be cached copies of third-party open source packages or proprietary first-party packages, and everything in between.

With ProGet, you get much more than just a place to store packages: 

{.docs}

- [Replication](/docs/proget/advanced/feed-replication)
- [Retention](/docs/proget/administration/retention-rules)
- [Connectors](/docs/proget/feeds/connector-overview)
- [Vulnerability Scanning](/docs/proget/compliance/vulnerabilities)
- [License Detection](/docs/proget/compliance/license-detection)
- [Cloud storage](/docs/proget/advanced/cloud-storage)
- And much more

The rest of this page explains more about what packages are, how to use them, and other commonly asked questions. Looking for a specific package type? Documentation for different package types can be found in the menu to your left.

## What's inside a package? {#whats-inside data-title="What's Inside a Package?"}

There's not a whole lot to a package: It's just a zip file containing the files you actually want to distribute, as well as a manifest file that describes the package itself (like who took some action on the package and when they did it). The specific layout of the zip file and manifest is referred to as a package format.

## How is a package different from an artifact? {#packages-v-artifacts data-title="How Is a Package Different from an Artifact?"}

Artifacts can be any type of file (e.g., .jar., .war, .dll, .rpm, .zip, .jpg). Artifacts are just files; no manifest data is included. You may have the file that you need, but not the context you may require.

Packages have a standards-defined format (e.g., NuGet, PyPi, etc.) and include not just the files but the metadata as well. [More information means more visibility and less confusion](https://inedo.com/proget/proget-vs-jfrog-artifactory).

## What package types does ProGet support? {#supported data-title="What Package Types does ProGet Support?"}

{.docs}

- [NuGet](/docs/proget/feeds/nuget)
- [PowerShell](/docs/proget/feeds/powershell)
- [Ruby Gems](/docs/proget/feeds/rubygem)
- [Visual Studio Extension (.vsix)](/docs/proget/feeds/vsix)
- [Maven (Java)](/docs/proget/feeds/maven)
- [npm (Node.js)](/docs/proget/feeds/npm)
- [Chocolatey (Windows/Machine)](/docs/proget/feeds/chocolatey)
- [Debian (apt)](/docs/proget/feeds/debian)
- [Helm (Kubernetes)](/docs/proget/feeds/helm)
- [PyPi (Python](/docs/proget/feeds/pypi)
- [RPM (yum)](/docs/proget/feeds/rpm)
- [Docker (containers)](/docs/proget/docker/private-registries)

Not seeing the package type you want? We do take [feature requests](https://inedo.com/open/feature-requests) seriously.

## Using Third-party/Open-source Packages {#third-party data-title="Using Third-party and Open-source Packages"}

Many times, your development will draw on third-party/open-source packages, like NuGet or Chocolatey. To use these packages in ProGet, you’ll first create a [feed](/docs/proget/feeds/feed-overview) for that package type. Then add a [connector](/docs/proget/feeds/connector-overview) to your ProGet feed and point ProGet at the correct URL. The feed will then populate with packages from the external source.

You can eliminate time wasted waiting for third-party packages to download by pulling a local copy of the package to ProGet. You can also [cache packages and/or metadata](/docs/proget/feeds/connector-overview#connector-caching) to ensure the packages you need are there when you need them.

## Creating and Using Proprietary Packages {#first-party data-title="Creating and Using Proprietary Packages"}

Many third-party package formats, like NuGet, can be created internally for proprietary use. [Using a tool like BuildMaster, you can apply the rigorous testing of CI/CD as you create .nupkg packages for private use.](/docs/buildmaster/platforms/dot-net/nuget) Check out the extensive documentation available for [more popular package types](/docs/proget/packages/what-is-a-package#supported) for instructions on creating proprietary packages.

Once you've created a package, there are four ways to publish it to ProGet for use:

{.docs}

- **Upload from disk**: uploads a pre-packaged package from disk
- **Push with the Client Tool**: uses the command-line tool, such as nuget.exe or npm.exe, to add a package to the local feed
- **Pull from another feed**: pulls a package from another feed of the same package type
- **Bulk package import/file copy**: imports multiple existing packages at once

For more specific instructions on how to add a package to a feed, while *inside ProGet*, click the Add Package button in the ProGet feed for that package type.

If there is a third-party package format designed for your specific case, we recommend you use it. In many cases, however, universal packages provide the best combination of simplicity, utility, and extensibility.

## Publishing Your Own Applications as Universal Packages {#universal-packages data-title="Publishing Your Own Applications as Universal Packages"}

The Universal Package format is very simple and can be used to package applications and components built with any technology: ASP.NET websites, NodeJS applications, Windows services, plug-ins for your applications, system configuration scripts, and so on. It's designed for both general-purpose use and as a platform for creating a new proprietary package format. You can also extend a universal package's manifest file with additional metadata (and then search using that metadata).

There are a lot of free and open-source tool options to help you create and publish packages to ProGet, either from your workstation, a build server, or anywhere else.

You can use any of these tools or libraries:

You can use any of these tools or libraries:

{.docs}

- [upack.exe Command-line Interface](/docs/upack/tools-and-libraries/upack-cli)   
- [UPackLib.NET library](/docs/upack/tools-and-libraries/upacklib-net)
- [Universal Package Explorer](/docs/upack/tools-and-libraries/upe)
- [Inedo ProGet Jenkins Plugin](https://plugins.jenkins.io/inedo-proget/)
- [Push to ProGet Visual Studio Extension](/docs/upack/tools-and-libraries/push-to-proget)

You can also upload hand-crafted package files to the ProGet the UI, or simply do a HTTP Post with your own tool/scripts using the [Universal Feed API](/docs/upack/feed-api/endpoints).

## Identifying and Versioning Packages {#versioning data-title="Identifying and Versioning Packages"}

One of the most important aspects of a package is that it is uniquely identifiable using a name and version. This simple, human-readable identification is what makes packages so easy to distribute and consume.

For example, “HDars-API 1.0.4” is version 1.0.4 of HDars-API, which is newer than “HDars-API 1.0.2”, older than “HDars-API 1.3.0”, and different than “HDars-Web 1.0.4”. “HDars-API” by itself is fairly meaningless, because it could refer to any version of HDars-API.

Universal packages (as well as some third-party packages) use the SemVer specification to describe the version number.

## Common Package Operations {#common-operations data-title="Common Package Operations"}

### Publish/Upload

To publish or upload a package to ProGet, follow these steps inside ProGet:
1.	Create a feed for that package type OR open the feed you want to upload the file to
2.	Next to the “Manage Feed” button on the right, click the ▼. The dropdown will display “Add Package.”
3.	Select the upload method of your choice from the pop-up window.

### Delete/Remove

To delete or remove a package to ProGet, follow these steps inside ProGet:
1.	Go to the Packages tab at the top ribbon. 
2.	Click the red X on the right of the package you wish to delete.
3.	Select “Yes, Delete Package” from the pop-up window. This is a permanent action. *Note:* You can optionally delete all versions of the package, which deletes all versions of that package only within that feed. To delete a package that has been promoted between different feeds, repeat these steps.

### Unlist/Deprecate

“Unlist” reduces visibility in searched. Packages won’t show up unless you enter the exact URL. “Deprecate” gives a warning to people who are using the client.

*Note: This is only supported in [NuGet](/docs/proget/feeds/nuget#deleting-and-unlisting).*

### Promote

Package promotion in ProGet lets you separate packages by quality. Promoting a package will leave the package in the source feed and duplicate it into the target feed. You can manually delete the “older” package later or configure retention rules to do it for you.

To promote a package to ProGet, follow these steps inside ProGet:

1.	Go to the package overview page for the package you wish to promote. 
2.	Hovering over the ▼ by “Download Package,” select “Promote Package” from the dropdown.
3.	Select the target feed from the dropdown.

Note: If there is no target feed, you will see an “unavailable feed” message displayed.
Note: If the ProGet administrator has configured a [promotion pipeline](/docs/proget/packages/package-promotion#create-promotion-pipeline), the target feed dropdown will be replaced with a single selection.
Note: If you do not have promotion permissions, you will not be able to promote packages.
