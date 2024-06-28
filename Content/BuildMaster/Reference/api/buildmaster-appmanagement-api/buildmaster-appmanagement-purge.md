---
title: "Purge Application Endpoint"
order: 2
---

The *Purge Application* is an endpoint in [BuildMaster's Application Management API](/docs/buildmaster/reference/api/buildmaster-appmanagement-api) that will purge an application.

:::(Info) (🚀 Quick Example: Purging an Application with Curl)
This example purges an application `myApp`, authenticating with the API key `abc12345`:

```
curl -X POST --header "X-ApiKey: abc12345" https://proget.corp.local/api/purge?application=myApp
```
:::
:::(info) (Deactivating an Application)
Note that you may choose to simply deactivate the application instead of completely purging it by using the  [Update Application Endpoint](/docs/buildmaster/reference/api/buildmaster-appmanagement-api/buildmaster-appmanagement-update)
:::
## Request Specification
To purge an existing application, simply `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication) and querystring parameter `application` of the name or id to purge.
```
POST /api/applications/purge?«querystring-parameters»
```

An empty body is acceptable, but you can post key/value pairs in the body instead of the querystring.


**Purging an application** requires the name (e.g. `myApp`) or id (e.g. `2`):

```
POST /api/applications/purge?application=myApp
```

```
POST /api/applications/purge?application=3
```

**Purging an application** with a body requires the name (e.g. `myApp`) or id (e.g. `2`):

```json
POST /api/applications/purge
{
    "name": "myApp"
}
```

## Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | the application will be purged. The body will be empty
| **400 (Invalid Input)** | indicates application parameter is missing
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authorization); the body will be empty
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged
