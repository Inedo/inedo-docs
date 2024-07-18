---
title: "CI Build Status Badges"
order: 6
---

Status badges are images that show the status of a build at a glance, often with links back to the original CI system that generated them. BuildMaster supports the generation of status images and links using the CI Badge API.

Badges are commonly used by open-source software because it typically relies on multiple build systems or tools working together in an automated fashion, with the added ability to immediately see whether or not a build, test, or deployment was successful. An example of this is Inedo's own extensions hosted on GitHub (e.g. [InedoCore](https://github.com/Inedo/inedox-inedocore#inedox-inedocore)) that display a build status badge that links back to our public instance of BuildMaster where the extensions are built and deployed. 

### Generating CI Badges 

BuildMaster allows external tools to query build status in order to generate a CI Badge, such as:
![success.svg](/resources/docs/success.svg){height="200" width="200"}

To access the CI Badge API, an API key with access to the API must be added. Then, the following URLs can be used to generate either the badge image or a link to the appropriate build/execution:

#### Image:

```
{buildmaster-host}/api/ci-badges/image?API_Key={yourApiKey}&$ApplicationName={appName}
```

#### Link:

```
{buildmaster-host}/api/ci-badges/link?API_Key={yourApiKey}&$ApplicationName={appName}
```

#### HTML Example

Using the above URLs with actual data, a badge containing an image with a link in HTML is generated as follows:

```
<a href="https://buildmaster.inedo.com/api/ci-badges/link?API_Key=badges&amp;$ApplicationId=2" rel="nofollow"><img src="https://buildmaster.inedo.com/api/ci-badges/image?API_Key=badges&amp;$ApplicationId=2" alt="Build status"></a>
```

#### Markdown Example

To generate the same badge as the HTML example above using Markdown, use:

```
[![Build status](https://buildmaster.inedo.com/api/ci-badges/image?API_Key=badges&$ApplicationId=2)](https://buildmaster.inedo.com/api/ci-badges/link?API_Key=badges&$ApplicationId=2)
```

## Badge API

The CI Badge API provides a simple way to allow external tools to query build status to generate a CI Badge with a helpful link directly to the build or deployment execution to which the status on the badge refers.

### API Usage

Each of these endpoints requires that an API key with CI Badge access be provided with each request. In the examples, a query string is used for simplicity, since most rendered versions are submitted directly to a web page, but you could also use a header, a form value, or a JSON property. See [API Access & API Keys](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-security#api-keys) for more information.

::: (INFO)
As a best practice, API keys used for the CI Badge API should _only_ allow access to the CI Badge API and not have access to other APIs, as they are visible to anyone with access to the external system.
:::

### Endpoint Specifications

This endpoint receives data entirely as key/value parameters, which can be specified both in the querystring (recommended) and in the body (`application/x-www-form-urlencoded`); duplicate keys result in undefined behavior.

#### Endpoint Arguments

The arguments for both endpoints are the same, an API key (supplied as `key` or `API_Key`), and additional OData-style intersectional (i.e. AND'ed) filters to locate a matching build:

| Argument | Description |
| --- | --- |
| `$ApplicationName` | _(indexed)_ The latest execution of the latest build of the latest release in the specified application name |
| `$ApplicationId` | _(indexed)_ The latest execution of the latest build of the latest release in the specified application ID |
| `$ReleaseNumber` | _(indexed)_ The latest execution of the latest build of the specified release number; it is recommended to supply an application specifier with this argument |
| `$BuildNumber` | _(indexed)_ The latest execution of the specified build; it is recommended to supply an application specifier with this argument |
| `_$CustomVariable_` | Using a custom variable name and value pair as an argument will locate a build with a matching variable value in context. Common examples are `$GitCommit`, `$Branch`, `$Tag` such that the first build found that matches this specified build variable and value will be used for the badge status. |

Refer to the [examples](/docs/buildmaster/reference/api/buildmaster-ci-cd-continuous-integration-server-badges#examples) section for more information and use-cases.

#### Image Endpoint

GET /api/ci-badges/image

If successful, returns the 200 status with the content of an SVG image (content type `image/svg+xml`, or `gzip` if compression is supported by the client) representing the status of the queried build, one of the following:


| Badge | Conditions |
| --- | --- |
| ![success.svg](/resources/docs/success%281%29.svg){height="" width=""} | Latest matching build execution completed successfully |
| ![pending.svg](/resources/docs/pending.svg){height="" width=""} | Latest matching build execution created but hasn't started yet |
| ![running.svg](/resources/docs/running.svg){height="" width=""} | Latest matching build execution is in progress |
| ![warning.svg](/resources/docs/warning.svg){height="" width=""} | Latest matching build execution completed with at least one warning |
| ![error.svg](/resources/docs/error.svg){height="" width=""} | Latest matching build execution completed with at least one error |
| ![unknown.svg](/resources/docs/unknown.svg){height="" width=""} | No matching builds found. |

This endpoint will always return a 200 unless the API key is not valid or unauthorized, resulting in a 403.

#### Link URL Endpoint

GET /api/ci-badges/link

If successful, returns the 303 redirect status with the `Location` header to a page within BuildMaster depending on the `page` argument:


| "Page" argument | Redirect Page |
| --- | --- |
| `build` | _(default)_ Build Overview page |
| `release` | Release Overview page |
| `application` | Application Overview page |
| `execution` | Execution details page, either for a specific execution ID or the latest execution to first stage for matching build |


A successful matching build will result in a 303 redirect, and other errors will result in a 400 or 403.

#### Examples

##### HTML
```HTML
<a href="https://buildmaster.inedo.com/api/ci-badges/link?key=badges&$ApplicationName=Windows" rel="nofollow">
  <img alt="Build status" src="https://buildmaster.inedo.com/api/ci-badges/image?key=badges&$ApplicationName=Windows" />
</a>
```

##### GitHub README Markdown
```Markdown
[![Build status](https://buildmaster.inedo.com/api/ci-badges/image?key=badges&$ApplicationName=InedoCore)](https://buildmaster.inedo.com/api/ci-badges/link?key=badges&$ApplicationName=InedoCore)
```

##### GitLab Project/Group
Under **Settings** » **General** of a project or group:

**Link:**
`https://buildmaster.inedo.com/api/ci-badges/link?key=badges&$ApplicationName=%{project_path}`

**Image URL:**
`https://buildmaster.inedo.com/api/ci-badges/image?Key=badges&$ApplicationName=%{project_path}`


::: (INFO)
If the application name doesn't match exactly, simply add an Application-scoped configuration variable that matches the project name, and filter by that variable name instead.
:::
