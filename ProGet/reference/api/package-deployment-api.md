---
title: Package Deployment API
sequence: 1000
keywords: proget, storage, cloud, amazon, azure
subtitle: Package Deployment API
---

Available starting in ProGet 5.2.26, the Package Deployment API allows users to send custom package deployment records to ProGet. See [Package Deployment](/docs/proget/advanced/package-deployment) for more information.

## Package Deployment Data Model

| Property | Details  |
|-|-|
| `Application` | (Required) *`string`* - the application or tool doing the deployment e.g. NuGet, BuildMaster, Some Custom Tool |
| `Description` | (Required) *`string`* - brief summary of deployment |
| `Target` | (Required) *`string`* - identifies where the package was installed, typically the server name |
| `FeedName` | (Required) *`string`* - the name of the package feed |
| `PackageName` | (Required) *`string`* - the name or ID of your package |
| `Version` | (Required) *`string`* - the version of the package deployed |
| `GroupName` | (Optional) *`string`* - the group, scope, or namespace of the package (this varies based on feed type)  |
| `UserName` | (Optional) *`string`* - name of the user performing the deployment, defaults to authenticated user |
| `Url` | (Optional) *`string`* - URL that links to more information about the deployment |
| `Date` | (Optional) *`ISO 8601 UTC date`* - date of deployment, or current date if not supplied |

## Package Deployment Endpoint

### Response Codes

|Code|Description|
|---|---|
| `200` | Success | 
| `403` | Invalid/unauthorized API key, or impersonated user not granted `Feeds_DownloadPackage` privilege | 
| `404` | Feed name cannot be found
| `400` | Invalid or missing data

### Requests
<tab-block>
<tab name="URL-Encoded Request">

POST `/api/package-deployment/`

##### Powershell Example
```
Invoke-RestMethod -Method Post -Uri 'https://{proget-server}/api/package-deployment/' -Body 'FeedName=DefaultNuGet&Application=ProfitCalc&Description=Deployed by Custom Release Server&Target=localhost&PackageName=Newtonsoft.Json&Version=12.0.3'
```

</tab>
<tab name="JSON Request">

POST `/api/package-deployment/`
Content-Type: `application/json`

##### Powershell Example
```
$data = @{
    FeedName='DefaultNuGet'
    Application='ProfitCalc'
    Description='Deployed by Custom Release Server'
    Target='localhost'
    PackageName='Newtonsoft.Json'
    Version='12.0.3'
}
$headers = @{
    'content-type' = 'application/json'
}
Invoke-RestMethod -Method Post -Uri 'https://{proget-server}/api/package-deployment/' -Body ($data | ConvertTo-JSON) -Headers $headers
```

</tab>
<tab name="Form Post">

POST `/api/package-deployment/`
Content-Type: `application/x-www-form-urlencoded`

##### Powershell Example
```
$data = @{
    FeedName='DefaultNuGet'
    Application='ProfitCalc'
    Description='Deployed by Custom Release Server'
    Target='localhost'
    PackageName='Newtonsoft.Json'
    Version='12.0.3'
}
$headers = @{
    'content-type' = 'application/x-www-form-urlencoded'
}
Invoke-RestMethod -Method Post -Uri 'https://{proget-server}/api/package-deployment/' -Body $data -Headers $headers
```

</tab>
</tab-block>

### Authentication

If you disallow anonymous access to your feed, you will need to create an [API Key](/docs/proget/administration/security/api-keys) that grants access to the Feed API.  You will need to include that key as a header named `X-ApiKey` and set it's value to that API key.

##### Powershell Example

```
$headers = @{
    'X-ApiKey': '{your api key}'
}
Invoke-RestMethod -Method Post -Uri 'https://{proget-server}/api/package-deployment/' -Body 'FeedName=DefaultNuGet&Application=ProfitCalc&Description=Deployed by Custom Release Server&Target=localhost&PackageName=Newtonsoft.Json&Version=12.0.3' -Headers $headers
```