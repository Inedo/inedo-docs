---
title: "Create/Update Release"
order: 1
---

*Create/Update Release* is available as both a `pgutil` command and an HTTP Request, and will create a new release or update properties on an existing release, and return a [ReleaseInfo Object](/docs/proget/reference-api/proget-api-sca#releaseinfo-object) object describing the new or updated release.

Note: this API endpoint functions by creating a new release when the version specified in the API request does not exist, else it updates an existing release.

:::(Info) (🚀 Quick Example 1: Setting a Release to Inactive with Curl)
This example updates version `1.2.3` of a release for a project named `myProject` by setting it's status to "inactive", authenticating with the API key `abc12345`:

````
curl -X POST -H "X-ApiKey: abc12345" -H "Content-Type: application/json" -d "{\"project\": \"myProject\", \"version\": \"1.2.3\", \"active\": false}" "https://proget.corp.local/api/sca/releases"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `sca releases create` command is coming soon. Similar to the HTTP Request, it will create a new release or update properties on an existing release. 
:::

## HTTP Request Specification
To create or update a release, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication) and a [ReleaseInfo Object](/docs/proget/reference-api/proget-api-sca#releaseinfo-object) object as the request body.

```
POST /api/sca/releases
```

**Creating a release** with a `ReleaseInfo` body requires the `project` name (e.g. `myProject`) and the `version` (e.g. `1.2.3`):

```json
POST /api/sca/releases

{
  "project":"myProject",
  "version":"1.2.3",
}
```

**Updating a release** with a `ReleaseInfo` body requires the `project` name (e.g. `myProject`), the `version` (e.g. `1.0.0`) and the property to update (e.g. `active` as `false`):

```json
POST /api/sca/releases

{
  "project":"myProject",
  "version":"1.0.0",
  "active":false,
}
```

## HTTP Response Specification

A successful (`200`) response body will contain a [ReleaseInfo Object](/docs/proget/reference-api/proget-api-sca#releaseinfo-object) object. For example, to creating a new release version `1.2.3` of a project named `myProject`, the request would return this:

```json
POST /api/sca/releases

{
  "version":"1.2.3",
  "active":true,
  "viewReleaseUrl":"https://proget.corp.local/projects/release?projectReleaseId=2",
  "viewIssuesUrl":"https://proget.corp.local/projects/release/issues?projectReleaseId=2"
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [ReleaseInfo](/docs/proget/reference-api/proget-api-sca#releaseinfo-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [ReleaseInfo](/docs/proget/reference-api/proget-api-sca#releaseinfo-object) object; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |