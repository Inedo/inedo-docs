---
title: Package Promotion and Deployment
sequence: 600
keywords: proget, storage, cloud, amazon, azure
subtitle: Package Promotion and Deployment
---

## Package Promotion ##

Packages can be *promoted* from one feed to another. This is useful for maintaining feeds with separate levels of quality (such as Development, Production, etc), and also to easily curate selected packages from an external source to an approved repository.

:::attention {.best-practice}
This feature is available in paid and trial ProGet editions.
:::
Check out our [Promote Packages with ProGet](https://www.youtube.com/watch?v=0uVDUt0wMjM) video for a quick look at some of the ways to use Package Promotion.

![](/resources/documentation/proget/package-promotion.png)

When a package is deployed from a feed using a tool (such as [BuildMaster](buildmaster) or [Hedgehog](hedgehog) ProGet records information about that deployment, such as the target and the user that performed the deployment.

![](/resources/documentation/proget/deployment-record.png)

## Custom Records ##

Consumers of any package type may customize the GET request with the following HTTP headers:

{.docs}

- **X-ProGet-Deployment-Application** - (Required) the application or tool doing the deployment e.g. NuGet, BuildMaster, Some Custom Tool
- **X-ProGet-Deployment-Description** - (Required) brief summary of deployment
- **X-ProGet-Deployment-Target** - (Required) string that identifies where the package was installed, typically the server name- **X-ProGet-Deployment-Url** - (Optional) URL that links to more information about the deployment
- **X-ProGet-Deployment-UserName** - (Optional) name of the user performing the deployment, defaults to authenticated user
- **X-ProGet-Deployment-Date** -(Optional) ISO 8601 UTC date of deployment, or current date if not supplied
