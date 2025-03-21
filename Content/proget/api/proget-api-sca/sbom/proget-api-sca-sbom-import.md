---
title: "Import SBOM"
order: 2
---

*Import SBOM* is available as an HTTP Request, and will import an existing [SBOM](/docs/proget/sca/builds/proget-sca-sbom-importing-exporting#what-is-a-software-bill-of-materials-sbom) document into ProGet and create corresponding project and release information from it.

:::(Info) (🚀 Quick Example: Importing SBOM with Curl)
This example imports a locally stored SBOM of version `1.2.3` of a project `myProject` as an `XML`, authenticating with the API key `abc12345`:

```bash
curl -X POST -H "X-ApiKey: abc12345" -H "Content-Type: text/xml" -d "@C:/Users/Inedo/myProject-1.2.3-bom.xml" https://proget.corp.local/api/sca/import
```
:::

## HTTP Request Specification
To import an SBOM document, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```plaintext
POST /api/sca/import
```

Note that the `Content-Type` header should either be `text/xml` (XML) or `application/json` (JSON) depending on the import format.

**Importing an SBOM** requires the `project` name (e.g. `myProject`), the `version` (e.g. `1.2.3`), and either an `XML` or `JSON` as the body (e.g. `sbom.xml`):

```plaintext
POST /api/sca/import

«contents of sbom.xml»
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | indicates the SBOM import was successful |
| **400 (Invalid Input)** | indicates the submitted SBOM document was not valid |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |
