---
title: "Analyze Release"
order: 4
---

*Analyze Release* is available as both a `pgutil` command and an HTTP Request, and will run an analysis of a single release, similar to clicking the "analyze" button in the user interface, on the release overview page.

:::(Info) (🚀 Quick Example: Analyzing a release with Curl)
This example analyzes release version `1.2.3` of a project named `myProject`, authenticating with the API key `abc12345`:

````
curl -X POST -H "X-ApiKey: abc12345" "https://proget.corp.local/api/sca/analyze-release?project=myProject&version=1.2.3"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `sca releases analyze` command is coming soon. Similar to the HTTP Request, it will run an analysis of a single release.
:::

## HTTP Request Specification
To analyze a release, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication)

```
POST /api/sca/analyze-release?project=«projectName»&version=«releaseVersion»
```

**Analyzing a release** requires the `project` name (e.g. `myProject` ) and release `version` (e.g. `1.2.3`) properties.

```
POST /api/sca/analyze-release?project=myProject»&version=1.2.3
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | analysis was successful |
| **400 (Invalid Input)** | indicates invalid or missing arguments; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |