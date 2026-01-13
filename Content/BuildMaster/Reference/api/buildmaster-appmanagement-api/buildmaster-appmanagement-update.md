---
title: "Update Application Endpoint"
order: 3
---

The *Update Application* is an endpoint in [BuildMaster's Application Management API](/docs/buildmaster/reference/api/buildmaster-appmanagement-api) that can update properties of an existing application and return an [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object confirming the updated application.

:::(Info) (🚀 Quick Example: Cloning an Application with Curl)
This example updates an application named `myApp` changing the value of `allowIssues` to `true`, authenticating with the API key `abc12345`:

```
curl -X POST --header "X-ApiKey: abc12345" https://buildmaster.corp.local/api/applications/update?name=myApp&allowIssues=true
```
:::

## Request
To update an existing application, simply `POST` to the URL with an [appropriate API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication) and a [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object as the request body.

````
POST /api/applications/update
````

Querystring parameters may be used for [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object properties instead of the Body

````
POST /api/applications/update?«ApplicationInfo-properties»
````

**Updating an application** with an `ApplicationInfo` body requires the name (e.g. `myApp`) and an additional property to be updated (e.g. `allowIssues` to `true`):

```json
POST /api/applications/update
{
    "name": "myApp"
    "allowIssues": true
}
```

**Deactivating an application** with an `ApplicationInfo` body requires the name (e.g. `myApp`) and the `active` property be set to `false`.

```json
POST /api/applications/update
{
    "name": "myApp"
    "active": false
}
```

**Moving an application to a new group** with an `ApplicationInfo` body requires the name (e.g. `myApp`) and the `groupName` property be set to the group name (e.g. `myGroup`). If the application currently belongs to a group it will be changed. 

If the application does not currently belong to a group, the new group name will be assigned. The group must currently exist in the BuildMaster instance or the request will return a `400` error (Application group not found). 

```json
POST /api/applications/update
{
    "name": "myApp"
    "groupName": myGroup
}
```
**Note**: To move all applications in one group to another, use this [Move All Applications](#bulk-move) script.

**Updating an application** with  querystring parameters requires the name (e.g. `myApp`) and an additional property to be updated (e.g. `allowIssues` to `true`):

```
POST /api/applications/update?name=myApp&allowIssues=true
```

## Response
A successful (`200`) response body will contain an [ApplicationInfo](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info) object. For example, to update an application named `myApp`, setting `allowIssues` to `true`, the request would return this:

```json
POST /api/applications/update

{
    "id":1,
    "name":"myApp",
    "active":true,
    "buildNumberScheme":"Sequential",
    "releaseUsage":"Required",
    "allowIssues":true
}
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain a [ApplicationInfo Object](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info)
| **400 (Invalid Input)** | indicates invalid or missing properties on the [ApplicationInfo Object](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#application-info); the body will provide some details as text
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/buildmaster/reference/api/buildmaster-appmanagement-api#authentication); the body will be empty
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged

## Sample Usage Scripts

<h3 id="bulk-move">Move All Applications in Group (Powershell)</h3>

This script will move all applications in the group `DevelopmentGroup` to the group `ProductionGroup`. 

```powershell
$baseUrl = "https://buildmaster.corp.local"
$apiKey = "a1b2c3d4e5"
$sourceGroup = "DevelopmentGroup"
$targetGroup = "ProductionGroup"

$headers = @{"X-ApiKey" = $apiKey}
$getListEndpoint = "$baseUrl/api/applications/list"
$updateGroupEndpoint = "$baseUrl/api/applications/update"

function Get-ApplicationList {
    $response = Invoke-RestMethod -Uri $getListEndpoint -Method POST -Headers $headers
    return $response
}

function Update-ApplicationGroup($appName, $targetGroup) {
    $updateParams = @{
        name = $appName
        groupName = $targetGroup
    }

    $updateResponse = Invoke-RestMethod -Uri $updateGroupEndpoint -Method POST -Headers $headers -Body $updateParams
    return $updateResponse
}

$applicationList = Get-ApplicationList
foreach ($app in $applicationList) {
    if ($app.groupName -eq $sourceGroup) {
        Write-Host "Updating $($app.name) from $($app.groupName) to $($targetGroup)..."
        Update-ApplicationGroup -appName $app.name -targetGroup $targetGroup
        Write-Host "Update complete."
    }
}
```