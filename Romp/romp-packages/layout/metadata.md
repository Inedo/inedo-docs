---
title: Metadata
subtitle: Package Metadata
sequence: 10
keywords: romp, metadata
---

At a minimum, a romp package must contain a `upack.json` file.

See the [Universal Package Metadata Specifications](/support/documentation/various/universal-packages/universal-feed-api#metadata) for a list of `upack.json` properties.

A Romp package may also include an additional metadata file, `rompPackage.json`, that defines package behavior.

### Romp Package Metadata Specification {#specification data-title="Romp Package Metadata Specification"}

Just like `upack.json`, a `rompPackage.json` file is a JSON object with the following optional properties:

| <!-- --> | <!-- -->|
| ---     | ---    |
| `minimumClient` | a *string* representing a three-part version number that describes the lowest version of the romp client that may install the package; when not specified, any client version may be used |
| `requiredExtensions` | an *array of strings* containing the names of extensions that must be installed in the romp’s extension; when not specified, the default extensions are InedoCore, Windows, and Linux <br/><br/> *note: if you define this, make sure to also specify the defaults if you wish those to be also required* |
| `machineOnly` | a *boolean* indicating whether the package may only be installed as a machine package; this defaults to false |

*However*, unlike `upack.json`, the `rompPackage.json` file may not contain any additional properties.
