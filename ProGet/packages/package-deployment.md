---
title: Tracking Package Deployments
sequence: 400
show-headings-in-nav: true
---

When a package is deployed from a feed using a tool (such as [BuildMaster](buildmaster)) ProGet records information about that deployment, such as the target and the user that performed the deployment.

![](/resources/documentation/proget/deployment-record.png)

## Custom Records {#custom-records data-title="Custom Records"}

Consumers of any package type may customize the Feed API GET request with the following HTTP headers:

{.docs}

- **X-ProGet-Deployment-Application** - (Required) the application or tool doing the deployment e.g. NuGet, BuildMaster, Some Custom Tool
- **X-ProGet-Deployment-Description** - (Required) brief summary of deployment
- **X-ProGet-Deployment-Target** - (Required) string that identifies where the package was installed, typically the server name         
- **X-ProGet-Deployment-Url** - (Optional) URL that links to more information about the deployment
- **X-ProGet-Deployment-UserName** - (Optional) name of the user performing the deployment, defaults to authenticated user
- **X-ProGet-Deployment-Date** - (Optional) ISO 8601 UTC date of deployment, or current date if not supplied

## Package Deployment API {#package-deployment-api data-title="Package Deployment API"}

If you are using a tool other than BuildMaster to deploy your packages, you can use the [Package Deployment API](/docs/proget/reference/api/package-deployment-api) to send package deployment records to ProGet.