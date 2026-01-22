---
title: "Query Package Promotions"
order: 1
---

:::(internal)
May not recognize/return existing grouped packages (e.g. npm packages with a @scope)
:::

The *Query Package Promotions* API is available as an HTTP Request, and returns a list describing the status of promoted packages.

:::(Info) (🚀 Quick Example: Querying with Curl)
This example queries a promoted package (`myNugetPackage`, version `1.2.3`) from one feed (`nuget-unapproved`) to another (`nuget-approved`), authenticating with the API key `abc12345`:

```bash
curl -X GET --header "X-ApiKey: abc12345" "http://proget.corp.local/api/promotions/list?package=Newtonsoft.Json&version=13.0.2"
```
:::

Note, it is not considered an error if the promoted package and it's history do not exist. 

## Authentication and API Keys { #authentication }

The following is a summary of access types and their corresponding requirements for various API keys types and endpoints within this API.

:::(info)
An API Key is not required to query package promotions.

When an API Key isn't specified, and the Anonymous user doesn't have "Feeds: View Feed" access, then a `401` authentication challenge will be issued. Otherwise, only the promotions the Anonymous user has access to view will be displayed.
:::

| Access Type | Requirements |
| --- | --- |
| System API Keys | `Use/Manage Feeds`
| Feed API Keys | `View/Download`
| Personal API Key | `Feeds_ViewFeed`
| No API Key | anonymous or authenticated user must have at least `Feeds_ViewFeed`

To specify an API Key, use the request header (`X-ApiKey`), querystring (`key`), or `api:«api-key»` as the username. See [API Key Usage](/docs/proget/api/apikeys#using-api-keys) to learn more.

## Request Specification
To query the status of promoted packages, simply `GET` to the URL with querystring parameters and an [appropriate API Key](/docs/proget/packages/package-promotion#authentication).

You may filter the results by specifying one or more parameters:

| Argument | Details |
| --- | --- |
|`fromFeed`| the feed that the package was promoted from |
|`toFeed`| the feed that the package was promoted to |
|`group`| used only by Universal and npm feeds; for npm, this is the "scope" |
|`package`| the package name of the promoted package
|`version`| the version of the promoted package

```plaintext
GET /api/promotions/list?fromFeed=«feed-name»&toFeed=«feed-name»&group=«group-name»&package=«package-name»&version=«version»
```

**Querying a specific promoted NuGet package** requires the package name (e.g. `myNugetPackage`), the version (e.g. `1.2.3`), the feed promoted from (e.g. `nuget-unapproved`) and the feed promoted to (e.g. `nuget-approved`)

```plaintext
GET /api/promotions/list?fromFeed=npm-development&toFeed=npm-production&group=myScope&package=myNpmPackage&version=1.2.3
```

## Response Specification
A successful (`200`) response body will contain an array of PromotePackage (see [PromotedPackage](#promoted-package)) objects. A `401` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [PromotedPackage](#promoted-package) objects |
| **400 (Invalid Input)** | indicates invalid or missing properties on the package; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/packages/package-promotion#authentication); the body will be empty |

## Data Specifications { #data-specifications }

### PromotedPackage Object Attributes { #promoted-package }

`PromotedPackage` is a set of key/value pairs used as output data for the Query Promoted Package endpoint as a JSON-formatted object.

| Property | Format | Notes
| --- | --- | ---
| `group` | string | representing the name or ID of the package. |
| `package` | string | representing the group of the package for Universal and Maven feeds and the scope of the package for npm feeds. This property is omitted for all other feed types. |
| `packageType` | string | representing the feed type of the package, one of: `upack`, `powershell`, `nuget`, `chocolatey`, `maven`, `npm`, `bower`, or `docker` |
| `version` | string | representing the version of the package. In ProGet 5.0.9 and later, you can specify `latest` or `latest-stable` for the `version` property. This will then promote the latest or latest non-prerelease version of the package. |
| `promotions` | array | An array of [PromotionData](#promotion-data) objects representing package promotions. |

#### JSON Object:

```json
{
  "group": "myScope",
  "package": "myPackage",
  "packageType": "npm",
  "version": "1.0.0",
  "promotions": [
    {
      // PromotionData Object
    }
  ]
}
```

### PromotionData Object Attributes { #promotion-data }

`PromotionData` is a subset of attributes on the [PromotedPackage](#promoted-package) Object that provide details of an individual promotion, used as output data for the **Query Promoted Package** endpoint.

| Property | Format | Notes
| --- | --- | ---
| `fromFeed` | string | representing the name of the feed the promotion originated from. |
| `toFeed` | string | representing the name of the feed the package was promoted to. |
| `date` | string | representing the UTC date of the promotion in ISO 8601 format (yyyy-MM-ddThh:mm:ss). |
| `user` | string | representing the user who promoted the package. Packages promoted via this API will by noted with `SYSTEM` as the user. |
| `comments` | string | containing comments submitted when the promotion occurred. |
| `id` | int |  representing a unique identifier of the promotion. |

#### JSON Object:

```json
{
  "fromFeed": "npm-unapproved",
  "toFeed": "npm-approved",
  "date": "2023-11-29T12:00:00",
  "user": "jsmith",
  "comments": "Package approved for production use",
  "id": 12345
}
```