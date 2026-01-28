---
title: "Virtual Packages in Universal Package Feeds"
nav-title: "Virtual Packages"
order: 3
---

A Virtual Package is a special type of Universal Package that contains no content (i.e. application files, components, metadata files, etc.). It's simply a package manifest file (i.e. `upack.json`) with instructions on where to download the content, contained in a ZIP archive with the `.vpack` extension.

When you upload a Virtual Package to a feed, it will appear and function as an ordinary universal package in most cases. When a user or client downloads the package from the feed, ProGet will transparently assemble the package file.

## Creating Virtual Packages

A Virtual Package is JSON-based Universal Package Manifest file with an additional `content` property that describes the package's content (i.e. what files will be placed in the `packages/` folder in the package file). This file, named `upack.json` must then be placed within a ZIP archive with the `.vpack` extension. 

For example, the following Virtual Package simply combines two other universal packages into one:

```
# upack.json
{
 "name": "HDARS.Combined",
 "version": "1.3.9",
 "contents": [ "HDARS.Web:1.3.9", "HDARS.API:1.3.9"  ]
}
```

This should be placed in a ZIP archive renamed to `.vpack` (e.g.`HDARS.Combined-1.3.9.vpack`).

ProGet will assemble the `HDARS.Combined` package file by taking the contents from the referenced packages (i.e. files under `package/` in the package file) and adding them to the `packages/` folder in the new package file. 

<style>
.vpackcompare td, .vpackcompare th  { border: solid 1px #ccc; }
</style>

<table class="vpackcompare">
<tr><th>HDARS.Web-1.3.9</th><th>HDARS.API-1.3.9</th><th>HDARS.Combined-1.3.9<sub>v</sub></th></tr>
<tr>
<td><pre>
upack.json
package/index.htm
package/logo.gif
</pre></td>
<td><pre>
upack.json
package/cgi-bin/api.py
</pre></td>
<td><pre>
upack.json
package/index.htm
package/logo.gif
package/cgi-bin/api.py
</pre></td>
</tr>
</table>

Note that the manifest files (`upack.json`) from the referenced packages are not used. The new manifest file (`upack.json`) will contain all of the same properties as the virtual package file, except the `contents` property:

```
# upack.json
{
 "name": "HDARS.Combined",
 "version": "1.3.9"
}
```

:::(Internal)
Seems like we should add some history entries here?
:::


### Using Virtual Paths

By default, ProGet will assemble referenced resources into the new package's content root (i.e. `packages/`). You can specify a subfolder by using a [Virtual Content Object](#virtual-content-object) with a `targetPath` property. 

For example, consider this virtual package that's assembled from 4 packages:

```
# ErpProduct.Initech-2.2.1.vpack
{
 "name": "ErpProduct.Initech",
 "version": "2.2.1",
 "contents": [
   "ErpProduct.Core:2.2.1",
   {
    "source": "Plugins.Initech:2.0.1",
    "targetPath": "custom/plugin"
   },
   {
    "source": "Plugins.SalesPipeline:2.1.0",
    "targetPath": "custom/plugin"
   },
   {
    "source": "Plugins.Workflows:2.1.0",
    "targetPath": "custom/plugin"
   }
  ]
}
```

The `ErpProduct.Core:2.2.1` package is specified as a string, but the other three packages are specified as objects with a `source` and `targetPath`. As such, those packages' contents will be written to the `package/{targetPath}/` folder instead of `package/`. 

Assuming that each of those packages had a single file, the assembled package might look like this:

```plaintext
package/erp.core.exe
package/custom/plugins/initech.dll
package/custom/plugins/salespipeline.dll
package/custom/plugins/workflows.dll
upack.json
```


### Using Files and Assets

In addition to packages, content from an asset directory or files from a url.

```
{
 "group": "initrode/vendors/abl",
 "name": "ABLast.AstDist",
 "version": "2.2.1",
 "contents": [
   {
    "virtualPath": "vendors/common/ast",
    "source": "initrode/vendors/abl/ABlast:2.2.1:ab60bf74fc8147ca41bd53bdb1defc3aae35bc91"
   },
   {
    "virtualPath": "common/logo/logo.png",
    "source": { 
       "url": "http://proget/endpoints/customer-assets/content/ast-logo.png"
    }
   },
   {
    "virtualPath": "common/logo/logo.png",
    "source": { 
       "feed": "customer-assets",
       "asset": "logos/ast-logo.png"
    }
   }
  ]
}
```

### Metacontents

By default, ProGet will assemble package contents to the `packages/` folder in the new package file. However, if you specify items in the `metaContents` folder, they will instead be written to the root folder in the package file.


## Usecases for Virtual Packages

There are two main use cases for virtual packages:

### Usecase: Bundling Multiple Packages

There are several cases where you may want to bundle a number of packages into a single, logical package.

:::(Info)
Let us say you have created an application that has been customized for six different clients by overwriting logos, JavaScript files, or other resources within the application. The "base application" is completely useless on its own, except for testing and quality assurance purposes, and the "resources" are even more useless. They must be combined with a specific version of the "base application" to be tested at all.
:::

This can be easily solved with a virtual package that "bundles" a specific version of the base application (`CrmAppBase`) and the client customization (`InitechApp`) into a single, logical package (`Initech.crm`).

When you deliver the package, it will be exactly what is needed. However, you can easily see from the metadata which versions/components were used to assemble the package.

### Usecase: Logical Packaging of External Files

Another way to use Virtual Packages is to reference known external files.

:::(Info)
For example, if your package contains large, immutable, multi-gigabyte assets (such as movies or map files), maintaining and transferring multiple versions of these packages is slow and requires a lot of disk resources. Instead, you can use file references to logically pack these resources into one package.
:::

You can also bundle these files together with your regular package contents by bundling them.

## Assembling Virtual Package Contents

When assembling a virtual package, ProGet uses the following logic:

1. The `contents` array is enumerated, and each resource is assessed as follows:
    A. If it's a file resource: the contents are downloaded, verified (if a hash is specified), and written to the target virtual path. A directory is created if it doesn't exist, and a file is ignored if it already exists.
    B. If it's a package resource: the current package feed is queried for the specified package (including hash). The contents are then extracted to the target virtual path, ignoring any existing files.

2. The` metaContents` array is enumerated; each resource is assessed as follows:
    A. If the virtualPath is `upack.json` or within the packages directory: the operation fails
    B. If it's a file resource: the contents are downloaded, verified (if a hash is specified), and written to the target virtual path. A directory is created if it doesn't exist, and a file is ignored if it already exists.
    C. If it's a package resource: the current feed is queried for the specified package (including hash). The contents are then extracted to the target virtual path, ignoring any existing files.

3. If any error occurs during assembly, then the entire operation fails.

Depending on the size of the referenced files and packages, package assembly may be resource-intensive and time-consuming. However, ProGet will save assembled packages to the feed's package store using a `.constructed` extension and will use this file for subsequent requests.

:::(Internal)
- verify behavior is documented (i.e. constructed by default)
- add support for asset directories and other feeds (:: separator?)
- verify .constructed files are actually deleted
- maybe, let users see constructed status?
:::


## Specifications & Data Objects

### Additional Manifest File Properties

| Property | Format |
| --- | --- |
| `contents`<sup>R</sup> | An _array_ of at least one item, containing any of the following:<br>&bull; _string_ - a package identification string<br>&bull; _string_ - an absolute URL to an HTTP or HTTPS file resource<br>&bull;    _object_ - [virtual content object](#virtual-content-object) <br><br>A _package identification string_ is a 3- or 4-part string, formatted as follows:<br>&bull; &laquo;group&raquo;/&laquo;package-name&raquo;:&laquo;version&raquo;<br>&bull; &laquo;group&raquo;/&laquo;package-name&raquo;:&laquo;version&raquo;:&laquo;sha1-hash&raquo; |
| `metaContents` | _same as contents property_ |

### Virtual Content Object

This object describes a remote file or folder within either the root of the content (`package/`) or package root (`/`) folder, depending on whether it's within the `contents` or `metaContents` property.

<table style="margin-top: 12px;"><thead><tr><th>Property</th><th>Format</th></tr></thead><tbody><tr><td><code>type</code></td><td>A <em>string</em> of either <code>virtualDirectory</code> or <code>virtualFile</code> that describes the content to be added. The default value is <code>virtualDirectory</code>, and is used only in the case of missing property. All other values (including null or empty) are invalid.</td></tr><tr><td><code>virtualPath</code></td><td>A <em>string</em> of a path to a file or folder within either the root of the content (<code>package/</code>) or package root (<code>/</code>) folder, depending on whether the object is within the <code>contents</code> or <code>metaContents</code> property.<br><br>A missing property, or null, empty, or "/" string means the root within that path.<br><br>When used in metaContent, a value of <code>upack.json</code> is invalid, as the manifest can never be virtualized. A value that begins with <code>package/</code> is also invalid for metaContent.</td></tr><tr><td><code>source</code></td><td>One of the following values:<ul class="docs"><li>package identification string</li><li><a href="#package-source-object">package source object</a></li><li><a href="#file-source-object">file source object</a></li></ul></td></tr></tbody></table>

### Package Source Object

This object describes where either a `virtualFile` or `virtualFolder` (as specified by the parent object) can be found.

| Property | Format |
| --- | --- |
| `group` | A _string_ of the referenced package group. When not specified, the empty group is used. |
| `name`<sup>R</sup> | A _string_ of the referenced package name. |
| `version`<sup>R</sup> | A _string_ of the referenced package version. |
| `hash` | A SHA-1 hash string of file contents  |
| `packagePath` | A _string_ containing the path within the package file (i.e. under the `/` directory of the archive file) the content can be found. A missing property or null value will default to `package/`. |

### File Source Object

This object describes where a `virtualFile` can be found.

| Property | Format |
| --- | --- |
| `url`<sup>R</sup> | A _string_ of an absolute HTTP/HTTPS resource where file contents can be found 
| `hash` | A SHA-1 hash string of file contents |
