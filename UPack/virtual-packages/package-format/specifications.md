---
title: Specifications
subtitle: Specifications
sequence: 10
keywords: proget, universal-packages, virtual
---

##Virtual Content Objective {#content data-title="Virtual Content Objective"}

This object describes a remote file or folder within either the contents root (`package/`) or package root (``/</``) folder, depending on whether it's within the `contents` or `metaContents` property.

|Property | Format |
| ----    | ------ |
| `type`  | A *string* of either `virtualDirectory` or `virtualFile` that describes the content to be added. The default value is `virtualDirectory`, and is used only in the case of a missing property. All other values (including null or empty) are invalid. |
| `virtualPath` | A *string* of a path to a file or folder within either the contents root (`package/`) or package root (`/`) folder, depending on whether the object is within the `contents` or `metaContents` property. <br /><br /> A missing property, or null, empty, or " string means the root within that path. <br /><br /> When used in metaContent, a value of <code>upack.json</code> is invalid, as the manifest can never be virtualized. A value that begins with `package/` is also invalid for metaContent. |
|`source` | One of the following values: <br /><br />&nbsp;&nbsp;&nbsp;&#8226;[package identification string](/support/documentation/upack/universal-packages/package-identification) <br /><br />&nbsp;&nbsp;&nbsp;&#8226;[package source object](#package-source-object) <br /><br />&nbsp;&nbsp;&nbsp;&#8226; [file source object](#file-source-object)

## Package Source Object {#source data-title="Package Source Object"}

This object describes where either a virtualFile or virtualFolder (as specified by the parent object) can be found.

| Property | Format |
| ---      | ----   |
|`group` | A *string* of the referenced package group. When not specified, the empty group is used. |   
| `name <sup>R</sup> ` | A *string* of the referenced package name. |
| `version<sup>R</sup>` | A *string* of the referenced package version. |
| `hash` | A [package hash string](/support/documentation/upack/universal-packages/validation-security) used to verify the package file |
| `packagePath` | A *string* containing the path within the package file (i.e. under the `/</` directory of the archive file) the content can be found. A missing property, or null value will default to `package/`. |

## File Source Object {#source data-title="File Source Object"}

This object describes where a virtualFile can be found.

|Property | Format |
| ---     | ---    |
|`url<sup>R</sup>` | A *string* of an absolute HTTP/HTTPS resource where file contents can be found |
| `hash`  | A hash string with the same format as a [package hash string](/support/documentation/upack/universal-packages/validation-security#package-hash-string) used to verify the file |
