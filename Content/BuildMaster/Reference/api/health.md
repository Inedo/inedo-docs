---
title: "BuildMaster Health API"
order: 5
---

The *Get BuildMaster Health* is an endpoint that will return a [BuildMasterHealth](#buildmasterhealth-object) object describing the health of a BuildMaster instance.

Using this endpoint does not require an API key and can be accessed by simply adding `/health` to your BuildMaster URL (e.g. `https://proget.corp.local/health`)

:::(Info) (🚀 Quick Example: Get the Health of a BuildMaster Instance with Curl)
This example will get the health of a BuildMaster instance with the URL `proget.corp.local`:

```
curl -H "Content-Type: application/json" -X GET https://proget.corp.local/health
```
:::

### Request Specification
To return the health status of a BuildMaster instance, simply `GET` to the URL. 

```
GET /health
```

### Response Specification
A successful (`200`) response body will return an [BuildMasterHealth](#buildmasterhealth-object) object. For example, to querying the health of a BuildMaster instance, the request would return this:

```json
GET /health

{
  "applicationName": "BuildMaster",
  "databaseStatus": "OK",
  "databaseStatusDetails": null,
  "extensionsInstalled": {
    "BuildMaster Core Extensions": "23.0.9",
    "BuildMaster": "23.0.9",
    "Inedo.SDK": "2.4.0",
    "Azure": "2.0.1",
    "AzureDevOps": "2.4.0",
    "Bitbucket": "2.4.0",
    "Docker": "2.4.3",
    "DotNet": "2.2.7",
    "Git": "2.4.0",
    "Gitea": "2.4.0",
    "GitHub": "2.4.2",
    "GitLab": "2.4.0",
    "InedoCore": "2.4.0",
    "Java": "2.2.0",
    "Jenkins": "2.4.0",
    "Jira": "2.4.2",
    "Node": "2.2.0",
    "Python": "2.2.0",
    "Scripting": "2.4.0",
    "TeamCity": "2.4.0",
    "Windows": "2.0.3"
  },
  "instanceName": null,
  "licenseStatus": "OK",
  "licenseStatusDetail": null,
  "versionNumber": "2023.9 (Build 1)",
  "releaseNumber": "23.0.9",
  "serviceStatus": "OK",
  "serviceStatusDetail": null
}
```

| Response | Details |
| --- | --- | 
| **200 (Success)** | body will contain a [BuildMasterHealth](#buildmasterhealth-object) object |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |

<h3 id="buildmasterhealth-object">BuildMasterHealth Object Attributes</h3>

`BuildMasterHealth` is a set of key/value pairs that describe the health status of a ProGet instance. It's used as output data for the [Get BuildMaster Health](#buildmaster-health-endpoint) endpoint as a JSON-formatted object.

| Property | Format | Notes
| --- | --- | ---
| `applicationName` | string | For this API it will be `BuildMaster` as default |
| `databaseStatus`| string | health state of the database. (`OK` or `Error`) |
| `databaseStatusDetails`| string? | a specific error message if `databaseStatus` is `Error` _(default `null`)_ |
| `extensionsInstalled`| object | describes the extentions installed in the instance and their versions (e.g. `"Azure": "2.0.1"`) |
| `instanceName`| string? | the name of the instance _(default `null`)_|
| `licenseStatus`| string | health state of the product license. (`OK` or `Error`) |
| `licenseStatusDetail`| string? | a specific error message if `licenseStatus` is `Error` _(default `null`)_ |
| `versionNumber`| string | current version number of the instance |
| `releaseNumber`| string | current release number of the instance |
| `serviceStatus`| string | health state of the service. (`OK` or `Error`) |
| `serviceStatusDetail`| string? | a specific error message if `serviceStatus` is `Error` _(default `null`)_ |