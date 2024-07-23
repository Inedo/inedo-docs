---
title: "Tracking Package Deployments"
order: 5
---

The Package Deployments feature records package deployment information (such as the target and user) from tools such as [BuildMaster](https://inedo.com/buildmaster/) and OctopusDeploy, and provides you with a package-centric view of deployments.

This feature allows you to determine exactly which package versions were deployed, when, by whom, with which tool, and so on. This simplifies the auditing process by keeping clear records directly in ProGet. It also helps you quickly find vulnerable servers [when a known vulnerability is identified](/docs/proget/sca/vulnerabilities): The feature in ProGet shows all servers running a particular package so you can quickly make updates.

There are no restrictions on this feature in the Free edition of ProGet. Package deployment records can be found, once configured, in the "History" tab on the Package Overview page.

## Custom Records

Consumers of any package type may customize the Feed API GET request with the following HTTP headers:

- **X-ProGet-Deployment-Application** - (Required) the application or tool doing the deployment e.g., NuGet, BuildMaster, Some Custom Tool
- **X-ProGet-Deployment-Description** - (Required) brief summary of deployment
- **X-ProGet-Deployment-Target** - (Required) string that identifies where the package was installed, typically the server name         
- **X-ProGet-Deployment-Url** - (Optional) URL that links to more information about the deployment
- **X-ProGet-Deployment-UserName** - (Optional) name of the user performing the deployment, defaults to authenticated user
- **X-ProGet-Deployment-Date** - (Optional) ISO 8601 UTC date of deployment, or current date if not supplied

## Package Deployment API 

If you are using a tool other than BuildMaster or OctopusDeploy to deploy your packages, you can use the Package Deployment API to extend this feature in ProGet and add custom package deployment records to ProGet from other tools. 

<h3 id="authentication">Authentication</h3>

If you disallow anonymous access to your feed, you will need to create an [API Key](/docs/proget/reference-api/proget-apikeys) that grants access to the Package Deployment API. This key requires the `Feeds_DownloadPackage` privilage.

To specify an API Key, use the request header (`X-ApiKey`), querystring (`key`), or `api:«api-key»` as the username. See [API Key Usage](/docs/proget/reference-api/proget-apikeys#using-api-keys) to learn more.

:::(Info) (🚀 Quick Example: Authenticating with PowerShell)
For example, to authenticate with the API key `abc12345`  to the query packages endpoint, you could specify the API key as follows:

```powershell
$headers = @{
    'X-ApiKey': 'abc12345'
}
Invoke-RestMethod -Method Post -Uri 'https://{proget-server}/api/package-deployment/' -Body 'FeedName=DefaultNuGet&Application=ProfitCalc&Description=Deployed by Custom Release Server&Target=localhost&PackageName=Newtonsoft.Json&Version=12.0.3' -Headers $headers
```
:::

<h3 id="package-deployment-object">PackageDeployment Object Attributes</h3>

**PackageDeployment** is a JSON object that corresponds to the fields on a Package Deployment Record. It's used as input data for the Package Deployment API endpoint as a JSON-formatted object.

| Property | Details  |
|-|-|
| `Application` | (Required) *`string`* - the application or tool doing the deployment (e.g., NuGet, BuildMaster, Some Custom Tool) |
| `Description` | (Required) *`string`* - brief summary of deployment |
| `Target` | (Required) *`string`* - identifies where the package was installed, typically the server name |
| `FeedName` | (Required) *`string`* - the name of the package feed |
| `PackageName` | (Required) *`string`* - the name or ID of your package |
| `Version` | (Required) *`string`* - the version of the package deployed |
| `GroupName` | (Optional) *`string`* - the group, scope, or namespace of the package (this varies based on feed type)  |
| `UserName` | (Optional) *`string`* - name of the user performing the deployment, defaults to authenticated user |
| `Url` | (Optional) *`string`* - URL that links to more information about the deployment |
| `Date` | (Optional) *`ISO 8601 UTC date`* - date of deployment, or current date if not supplied |

### Package Deployment Endpoint

#### HTTP Request Specification

To set a package's status, simply `POST` to the URL with  an [appropriate API Key](#authentication) and a [PackageDeployment](#package-deployment-object) object as the request body.
```
POST /api/package-deployment/
```
#### HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | indicates the package deployment record was added
| **400 (Invalid Input)** | indicates invalid or missing properties on the input; the body will provide some details as text
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](#authentication); the package status will not be updated
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message

### Sample Usage Scripts

#### Add Deployment Record (Powershell)
This script will add a deployment record to the application `ProfitCalc`, using `application/json` as the Content-Type.

```powershell
Invoke-RestMethod -Method Post -Uri 'https://{proget-server}/api/package-deployment/' -Body 'FeedName=DefaultNuGet&Application=ProfitCalc&Description=Deployed by Custom Release Server&Target=localhost&PackageName=Newtonsoft.Json&Version=12.0.3'
```

#### Add Deployment Record (Powershell)
This script will add a deployment record to the application `ProfitCalc`, using `application/x-www-form-urlencoded` as the Content-Type.

```powershell
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

