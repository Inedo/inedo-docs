---
title: "Universal Packages"
order: 2
---

A Universal Package is a file that contains other files such as programs, configurations, application components, plugins, libraries, or any other kind of content. It also contains a manifest file that uniquely identifies the package with a name/version and optionally describes its contents.

Unlike other packaging formats (NuGet, Rpm, etc.), Universal Packages were not designed for a specific use case (.NET libraries, Linux applications, etc.) and were designed to be as simple to use as possible.

You can create Universal Packages manually by hand or by using one of the [free and open Universal Packaging Tools](#tools).

## Universal Package Format

There isn't much to a universal package file; there are just two requirements:
1. is a ZIP archive format with a `.upack` file extension
2. has a manifest file in the root directory named `upack.json` with a few required fields

For example, a very basic Universal Package for `my-app` version `1.3.9` might look like this:

<style>
.upacksample td, .upacksample th  { border: solid 1px #ccc; padding: 0 40px 0 20px; }
</style>
<table class="upacksample">
<tr><th>Files my-app-1.3.9.upack</th><th>Content of upack.json</th></td>
<tr><td>
<pre>
upack.json
packages/my-app.config
packages/my-app.exe
packages/my-app.dll
</pre>
</td><td>
<pre>
{
 "name": "my-app",
 "version": "1.3.9"
}
</pre>
</td></tr><table>

You could create this package file manually or using one of the free and open-source [Universal Packaging Tools](#tools). For example:

```bash
$ upack pack .\my-app --name=my-app --version=1.3.9
```

That command will package the `.\my-app` folder into the `my-app-1.3.9.upack` ZIP archive file.  See the [upack CLI documentation](https://github.com/Inedo/upack) to learn more.


### Package Content and Metacontent

Files contained in the `package/` folder within a Universal Package  ZIP archive are referred to as *content*. When you "install" or "unpackage" a Universal Package to a folder on disk, that folder will only contain the files from the ZIP archive's `package/` folder.

For example:

```bash
$ upack unpack my-app-1.3.9.upack .\my-app
```

That command will basically do the opposite of the `pack` command and write the content files to the `.\my-app` folder.


All other files within a Universal Package (including `upack.json`) are referred to as *metacontent*. For most use cases, you will never need to use or access metacontent, but it might come in handy for advanced scenarios like embedding installation scripts, build logs, Software Bill of Material (SBOM) files, etc.


## Manifest (upack.json) Specification { #manifest }

The `upack.json` file is a JSON object with the following properties:

### Required or Implicit Properties { #required-properties }

| Property | Format |
| --- | --- |
| `group` | A _string_ of 0 to 250 characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), periods (.), forward-slashes (/), and underscores (\_); may not start or end with a forward-slash; if not specified, the group name will be considered an empty string. |
| `name`<sup>R</sup> | A _string_ of 1 to 50 characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), periods (.), and underscores (\_). |
| `version`<sup>R</sup> | A _string_ representing a [SemVer2 Semantic Version](http://semver.org/spec/v2.0.0.html); is a three-part, dot-separated specification. |

### Additional Descriptive Properties (Optional) { #descriptive-properties }

| Property | Format |
| --- | --- |
| `title` | A _string_ of no more than 50 characters. |
| `projectUrl` | A _string_ of an absolute URL where more about the package can be found |
| `icon` | A _string_ of an absolute URL pointing to an image to be displayed in a UI (at both 64px and 128px); if `package://` is used as the protocol, it references a package in the image within the package instead |
| `description` | A _string_ containing any number of characters; will be formatted as Markdown in the UI. |
| `shortDescription` | A _string_ containing up to 1000 characters; is a short summary that will be displayed instead of the description in lists and is not Markdown-formatted. |
| `tags` | An _array of strings_, each being 1 to 50 characters: numbers (0-9), upper- and lower-case letters (a-Z), dashes (-), periods (.), and underscores (\_). Tags may not start with a number and must be unique within the array. |
| `dependencies` | An _array of dependency specifier strings_, each of which may be formatted as follows:<br>&bull; «package-name»  <br>&bull; «group»/«package-name»<br>&bull; «group»:«package-name»<br>&bull; «group»/«package-name»:«version-range»<br>&bull; «group»:«package-name»:«version-range»<br>&bull; «group»:«package-name»:«version-range»:«sha1-hash»<br><br>«version-range» can be one of these:<br>&bull;  Not specified (or \*) to indicate the latest version<br> &bull;   SemVer2 Version Number<br>&bull;   Range of version numbers specified with interval notation, such as <br>    &bull; \[1.0.0,2.0.0) to mean every version between 1.0.0 and 2.0.0 (but not 2)<br>    &bull;  \[3.0.0,\] to mean every version after 3.0.0 |

### Audit-based Properties (Optional)  { #audit-based-properties }

| Property | Format |
| --- | --- |
| `createdDate` | A _string_ representing the UTC date when the package was first created, in ISO 8601 format (yyyy-MM-ddThh:mm:ssZ) |
| `createdReason` | A _string_ describing the reason or purpose of the creation  <br>  <br>_For example, [BuildMaster](https://inedo.com/buildmaster) uses_ `{Application Name} {Release Number} #{Package Number} (ID{Execution-Number})` |
| `createdUsing` | A _string_ describing the mechanism the package was created with; there are no format restrictions, but we recommend treating it like a User Agent string and including the tool name and version  <br>  <br>_For example, BuildMaster uses_ `BuildMaster/5.6.11` |
| `createdBy` | A _string_ describing the person or service that performed the installation  <br>  <br>_For example, BuildMaster uses the user who triggered the deployment or SYSTEM if it was a triggered/scheduled deployment_ |
| `repackageHistory` | An _array_ containing package identification strings or objects with the following properties:<br><br>&bull; `id`<sup>R</sup> - a _package identification string_<br>&bull; `date` - A _string_ representing the UTC date when the package was repackaged, in ISO 8601 format (yyyy-MM-ddThh:mm:ssZ)<br>&bull; `reason` - A _string_ describing the reason or purpose of the repackaging<br>&bull; `using` - A _string_ describing the mechanism the package was repackaged with; there are no format restrictions, but we recommend treating it like a User Agent string and including the tool name and version<br>&bull; `by` - A _string_ describing the person or service that performed the repackage<br>&bull; `url` - A _string_ where more information about the repackaging can be found, typically to logs within the tool that performed it<br><br>A _package identification string_ is a 3- or 4-part string, formatted as follows:<br>&bull; «group»/«package-name»:«version»<br>&bull; «group»/«package-name»:«version»:«sha1-hash»<br><br>You may include other properties in the object, but it is recommended to prefix the names with an underscore (\_). |

An <code>R</code> denotes a required property.

### Additional Metadata { #additional-metadata }

This object may contain additional properties as needed. However, if you need to add additional metadata, it's **strongly recommended** that you prefix these properties with an underscore (_)... just on the off-chance that a property you add will exist in a future version of the specification, or is returned in other metadata queries.

## Example Manifest Files (upack.json) { #examples }


### Using Extended Fields

The following manifest adds a `group` (which is a part of the package's identifier) and two custom properties (`_sourceRoot`, `_deployTarget`). Note how they are prefixed with an underscore as to avoid future naming conflicts.


```
{
 "group": "virtudyne/simdesk",
 "name": "var-index-service",
 "version": "5.3.9",
 "_sourceRoot": "$/global/vindex/branches/v5-hotfix"
 "_deployTarget": "/var/vsimdesk/vindex"
}
```


### Full/Complete Manifest

The following manifest uses just about every property available; note how the description is markdown formatted.

```
{
 "group": "initrode/vendors/abl",
 "name": "ABLast",
 "version": "2.2.1",
 "title": "ast distribution files for ABL",
 "icon": "package://ablast.svg",
 "description": "This contains [ast distro](http://initrode-net.local/ast) files specific to ABL",
 "dependencies": [ "initrode/vendors-common:ast-common:2.0.0" ],
 "createdDate": "2017-11-09T04:03:01Z",
 "createdReason": "ABLast v2.2.1 #18 (ID8843) ",
 "createdUsing": "BuildMaster/5.6.11",
 "createdBy": "THoven"
}
```


### Inedo Extensions (Plugins) Manifest

The following is the manifest for our AWS extension (i.e. Plugin) package which uses array-based custom properties. You can also see that this particular manifest was repackaged as part of a CI/CD process.

```
{
  "group": "inedox",
  "name": "AWS",
  "version": "3.1.0",
  "title": "Amazon AWS",
  "description": "Contains a ProGet Package Store backed by Amazon S3, and operations for working with S3 storage in Otter and BuildMaster.",
  "icon": "https://inedo.com/den/logos/AWS.png",
  "_inedoSdkVersion": "3.1.0",
  "_inedoProducts": [ "BuildMaster", "Otter", "ProGet" ],
  "_targetFrameworks": [ "net452", "net6.0", "net8.0" ],
  "repackageHistory": [
    {
      "id": "inedox:AWS:3.1.0-CI.2",
      "using": "ProGet/24.0.17"
    },
    {
      "id": "inedox:AWS:3.1.0-RC.2",
      "using": "ProGet/24.0.17"
    }
  ]
}
```



## Universal Packaging Tools { #tools} 

:::(Internal)
### pgutil upack

Coming soon.
:::

### UPack Command-line Interface

`upack` is a cross-platform command-line tool for creating and installing universal packages; you can also see which packages are installed on a machine.


* [Download upack (github.com)](https://github.com/Inedo/upack/releases)
* [upack Source Code (github.com)](https://github.com/Inedo/upack)



### Inedo.UPack

Inedo.UPack is a .NET library that makes it easy to create, read, and install packages on a server, as well as query installed packages or search packages in a remote feed on ProGet.

* [Inedo.UPack NuGet Package (nuget.org)](https://www.nuget.org/packages/Inedo.UPack)
* [Inedo.UPack Source Code (github.com)](https://github.com/Inedo/Inedo.UPack)

### Deprecated & Older Tools

Over the years, users have gotten more comfortable using Command-line tools. As such, we decided to abandon some of the earlier Universal Package tools we created.

Although we do not maintain these tools anymore, you may find them useful. If you think we should revive/rebuild them, just [let us know](https://forums.inedo.com/).

* **Universal Package Explorer** is a Windows desktop application that allows you to easily create, view, and publish universal packages. You can load a `.upack` file from disk or directly from a ProGet universal feed.
  * [Download Universal Package Explorer (github.com)](https://github.com/Inedo/UniversalPackageExplorer/releases)
  * [Universal Package Explorer Source Code (github.com)](https://github.com/Inedo/UniversalPackageExplorer)
* **Push to ProGet Visual Studio Extension** allows you to publish universal packages directly from Visual Studio. 
  * Install in Visual Studio (Tools > Extensions and Updates > Online > Search for "ProGet" > Download, Install, and Restart Visual Studio)
  * [download the extension from the Visual Studio Gallery directly](https://marketplace.visualstudio.com/items?itemName=vs-publisher-1463233.PushtoProGet).
* **ProGet Jenkins Plugin** integrates ProGet with Jenkins allowing Jenkins jobs to create and upload, or download and extract, universal packages. It still works, but it's no longer actively maintained.
  * See [ProGet Jenkins Plugin](https://plugins.jenkins.io/inedo-proget/) 
  
  