---
title: "List Application Endpoint"
order: 1
---

The *List Application* is an endpoint in [BuildMaster's Application Management API](/docs/buildmaster/reference/api/buildmaster-appmanagement-api) that will return an array of [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) objects listing all applications

:::(Info) (🚀 Quick Example: Listing Applications with Curl)
This example lists all applications, authenticating with the API key `abc12345`:

```
curl -X POST --header "X-ApiKey: abc12345" https://buildmaster.corp.local/api/applications/list
```
:::

## Request Specification
To list all applications, simply `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication).

```
POST /api/applications/list
```

## Response Specification
A successful (`200`) response body will contain an array of [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) objects. For example, the request would return this:

```json
POST /api/applications/list

[
  {
    "id":1,
    "name":"myApp1",
    "active":true,
    "groupName":"MyGroup"
    "buildNumberScheme":"Sequential",
    "releaseUsage":"Required",
    "allowIssues":false
  },
  {
    "id":2,
    "name":"myApp2",
    "active":true,
    "groupName":"OtherGroup"
    "buildNumberScheme":"DateTimeBased",
    "releaseUsage":"Required",
    "allowIssues":true
  },
  { ... } // other applications
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [ApplicationInfo Object](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication)
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication); the body will be empty
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged
