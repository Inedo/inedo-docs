---
title: "Inedo SDK Versions & Release Notes"
order: 1
---

The Inedo SDK is used to build extensions to Inedo's core products (BuildMaster, ProGet,  Otter) that implement core capabilities or add functionality. As new features are introduced into the core products, new components will be added to the SDK.

To keep track of which version of the SDK is compatible with which version of each product:
- the Inedo SDK is independently versioned using a three-part version number
- extensions are built against a specific version the Inedo SDK
- each product is built against a specific version of the Inedo SDK

Inedo products that are built against a specific version of the SDK can use extensions that were built against  earlier versions, so long as they have the same major release number.

For example:
* ProGet 5.3.21 targets SDK 1.10 and can use extensions built for SDK 1.10, 1.9, 1.8, etc.
* Otter 2022.0 targets SDK 2.0, and will *not* use extensions built against SDK 1.10

##  SDK Versioning
:::(Info) (Best Practice)
You should target the latest version of the SDK, unless you need your extension to be loaded in a specific earlier product version.
:::

The Inedo SDK is *semantically versioned*, which means that:
 * Major versions (e.g. 1.10 to 2.0) may remove components and introduce noncompatible API changes
 * Minor versions (e.g. 1.10 to 1.11) may introduce new components, deprecate (with a `Obsolete` attribute) old components, and introduce other backwards-compatible API changes
 * Patch versions (e.g. 1.10.0 to 1.10.1) introduce bugfix changes that do not affect the visible API


##  Product Compatibility
The below table shows the *minimum* version of each Inedo Product that the Inedo SDK is compatible with.

:::(Internal) (Update Instructions)
**For a new SDK version**, use this row template: `| 1.13.\*     | *n/a*   | *n/a*  | *n/a*`

**When implementing SDK in a product**, replace `*n/a*` with the product version number, then replace all `*n/a*` below that version as well, since it be compatible with those. For example, when BuildMaster 7.0.0 added support for SDK 1.12,  `7.0.0` replaced 5 `*n/a*` (1.11, 1.10, 1.9, 1.8).
:::

### SDK v3 (3.0)
| SDK Version | ProGet | BuildMaster | Otter
| --- | --- | --- | --- 
| 3.1.* | 2024.20 | 2024.0 | 2024.0
| 3.0.\*     | 2024.0      | *n/a*  | *n/a* 


### SDK v2 (2.0)
| SDK Version | ProGet | BuildMaster | Otter
| --- | --- | --- | --- 
| 2.4.\*     | *n/a*       | 2023.0  | 2023.0
| 2.3.\*     | 2023.0       | 2023.0 | *n/a*
| 2.2.\*     | 2023.0       | 2022.0  | *n/a*
| 2.1.\*     | 2022.3   | *n/a*  | 2022.6
| 2.0.\*     | 2022.0   | *n/a*  | 2022.0


### SDK v1 (1.0 to 1.14)
| SDK Version | ProGet | BuildMaster | Otter
| --- | --- | --- | --- 
| 1.14.\*     | 6.0.8 | *n/a*  | 3.0.19
| 1.13.\* | 6.0.0 | 7.0.12 | 3.0.19
| 1.12.\*     | 6.0.0   | 7.0.0  | 3.0.7
| 1.11.\*     | 5.3.23  | 7.0.0  | 3.0.2
| 1.10.\*     | 5.3.21  | 7.0.0  | 3.0.0
| 1.9.\*      | 5.3.12  | 7.0.0  | 3.0.0
| 1.8.\*      | 5.3.0   | 7.0.0  | 3.0.0
| 1.7.\*      | 5.2.4   | 6.2.0  | 3.0.0
| 1.6.\*      | 5.2.4   | 6.1.16 | 3.0.0
| 1.5.\*      | 5.2.4   | 6.1.12 | 2.2.7
| 1.4.\*      | 5.2.4   | 6.1.11 | 2.2.5
| 1.3.\*      | 5.2.4   | 6.1.10 | 2.2.4
| 1.2.\*      | 5.2.4   | 6.1.7  | 2.2.2
| 1.1.\*      | 5.1.0   | 6.1.0  | 2.1.0
| 1.0.\*      | 5.0.0   | 6.0.0  | 2.0.0

<sup>Note that the Linux versions of Inedo products (i.e. from ProGet 5.3.12+, BuildMaster 7.0.0+, and Otter 3.0.0+) only supports extensions using SDK 1.9+</sup>

The table will be expanded as new versions are released.
