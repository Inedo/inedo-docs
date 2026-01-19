---
title: "Export SBOM"
order: 1
---

*Export SBOM* is available as an HTTP Request, and will generate an [SBOM](/docs/proget/sca/builds/proget-sca-sbom-importing-exporting#what-is-a-software-bill-of-materials-sbom) document of a project and its build information in ProGet.

:::(Info) (🚀 Quick Example: Generating an SBOM with curl)
This example exports an SBOM of version `1.2.3` of a project `myProject` as an `XML`, authenticating with the API key `abc12345`:

```bash
curl -X GET -H "X-ApiKey: abc12345" "https://pg02.phs.inedo.com/api/sca/export?project=myProject&version=1.2.3&format=xml"
```
:::

## HTTP Request Specification
To generate an SBOM document, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/sca/export?project=«projectName»&version=«versionNumber»&format=«xml/json»
```

**Generating an SBOM** requires the `project` name (e.g. `myProject`), the `version` (e.g. `1.2.3`), and either an `XML` or `JSON` as the format (e.g. `json`):

```json
GET /api/sca/export?project=myProject&version=1.2.3&format=json
```

## HTTP Response Specification

A successful `200` response body will contain an SBOM in either `XML` or `JSON`. For example, to export an SBOM of version `1.2.3` of `myProject`:

In `JSON`, the request would return:

```json
{
  "bomFormat": "CycloneDX",
  "specVersion": "1.4",
  "metadata": {
    "timestamp": "2024-02-09T08:37:13Z",
    "tools": [
      {
        "vendor": "Inedo",
        "name": "ProGet",
        "version": "23.0.27.5"
      }
    ],
    "component": {
      "type": "null",
      "name": "myProject",
      "version": "1.2.3",
      "description": "This is a test project",
      "licenses": []
    }
  },
  "components": [
    {
      "type": "library",
      "name": "Newtonsoft.Json",
      "version": "13.0.3",
      "licenses": [
        {
          "license": {
          "id": "MIT"
          }
        }
      ],
      "purl": "pkg:nuget/Newtonsoft.Json@13.0.3",
      "externalReferences": [
        {
          "url": "https://proget.corp.local/packages/from-purl?purl=pkg%3anuget%2fNewtonsoft.Json%4013.0.3",
          "type": "website"
        }
      ]
    }
  ]
}
```

In `XML`, the request would return:

```xml
﻿<?xml version="1.0" encoding="utf-8"?>
<bom xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://cyclonedx.org/schema/bom/1.4">
  <metadata>
    <timestamp>2024-02-09T08:41:26.8462505Z</timestamp>
    <tools>
      <tool>
        <vendor>Inedo</vendor>
        <name>ProGet</name>
        <version>23.0.27.5</version>
      </tool>
    </tools>
    <component type="Null">
      <name>myProject</name>
      <version>1.2.3</version>
      <description>This is a test project</description>
    </component>
  </metadata>
  <components>
    <component type="library">
      <name>Newtonsoft.Json</name>
      <version>13.0.3</version>
      <licenses>
        <license>
          <id>MIT</id>
        </license>
      </licenses>
      <purl>pkg:nuget/Newtonsoft.Json@13.0.3</purl>
      <externalReferences>
        <reference type="website">
          <url>https://proget.corp.local/packages/from-purl?purl=pkg%3anuget%2fNewtonsoft.Json%4013.0.3</url>
        </reference>
      </externalReferences>
    </component>
  </components>
</bom>
```

A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication).
