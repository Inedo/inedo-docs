---
title: "Create/Update Build"
order: 1
---

*Create Build* is available as both a `pgutil` command and an HTTP Request, and will create a new build or update properties on an existing build, and return a [BuildInfo Object](/docs/proget/reference-api/proget-api-sca#buildinfo-object) object describing the new or updated build.

Note: this API endpoint functions by creating a new build when the version specified in the API request does not exist, else it updates an existing build.

:::(Info) (🚀 Quick Example: Creating a new build with pgutil)
This example creates build `1.2.3` of the project `myProject`

```bash
pgutil builds create --build=1.2.3 --project=myProject 
```
:::

## Command Specification (CLI)
The `builds create` command is used to create a new build.

The `--project` and `--build` options are always required.

**Creating a build** requires the project name (e.g. `myProject`) and the build number to be created (e.g `1.2.3`)
```bash
pgutil builds create --build=1.2.3 --project=myProject 
```

## HTTP Request Specification
To create or update a build, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication) and a [BuildInfo Object](/docs/proget/reference-api/proget-api-sca#buildnfo-object) object as the request body.

```plaintext
POST /api/sca/releases
```

## HTTP Response Specification

A successful (`200`) response body will contain a [BuildInfo](/docs/proget/reference-api/proget-api-sca#buildinfo-object) object. For example, to creating a new build version `1.2.3` of a project named `myProject`, the request would return this:

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
| **200 (Success)** | body will contain a [BuildInfo](/docs/proget/reference-api/proget-api-sca#buildinfo-object) object |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [BuildInfo](/docs/proget/reference-api/proget-api-sca#buildinfo-object)  object; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |