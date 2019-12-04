---
title: CI Build Status Badges
keywords: buildmaster, builds, ci-badges
sequence: 500
show-headings-in-nav: true
---

<style type="text/css">
    .badge {
        max-width: 150px;
    }
</style>

Status badges are images that show the status of a build at a glance, often with links back to the original CI system that generated them. BuildMaster supports generation of status images and links using the CI Badge API.

Badges are commonly used by open-source software as it typically relies on multiple build systems or tools working together in automated fashion, with the added ability to instantly see whether a build, test, or deployment was successful or failed. As an example, Inedo's own extensions that are hosted on GitHub (e.g. [InedoCore](https://github.com/Inedo/inedox-inedocore#inedox-inedocore)) display a build status badge that links back to our public instance of BuildMaster where the extensions are built and deployed. 

#### Generating CI Badges {#generating data-title="Generating CI Badges"}

BuildMaster allows external tools to query build status in order to generate a "CI Badge", such as:

<img alt="BuildMaster Build Success" class="badge" src="/resources/documentation/buildmaster/6/ci/success.svg" />

To access the CI Badge API, an API key must be added with access to the API. Then, the following URLs can be used to generate either the badge image or a link to the corresponding build/execution:

##### Image:

```
{buildmaster-host}/api/ci-badges/image?API_Key={yourApiKey}&$ApplicationName={appName}
```

##### Link:

```
{buildmaster-host}/api/ci-badges/link?API_Key={yourApiKey}&$ApplicationName={appName}
```

#### HTML Example

Using the URLs above with actual data, a badge that encompasses an image with a link in HTML is generated as follows:

```
<a href="https://buildmaster.inedo.com/api/ci-badges/link?API_Key=badges&amp;$ApplicationId=2" rel="nofollow"><img src="https://buildmaster.inedo.com/api/ci-badges/image?API_Key=badges&amp;$ApplicationId=2" alt="Build status"></a>
```

#### Markdown Example

To generate the same badge as the HTML example above using Markdown, use:

```
[![Build status](https://buildmaster.inedo.com/api/ci-badges/image?API_Key=badges&$ApplicationId=2)](https://buildmaster.inedo.com/api/ci-badges/link?API_Key=badges&$ApplicationId=2)
```

#### Badge API {#badge-api data-title="CI Badge API"}

The badge specifiers are not limited to applications and may be further filtered based on commit IDs, branches, and more. For more information on how to configure and query a badge, visit the [CI Badge API documentation](/docs/buildmaster/reference/api/ci-badge).