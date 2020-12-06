---
title: Tracking Package Deployments
sequence: 400
show-headings-in-nav: true
---

The Package Deployments feature records package deployment information (like the target and the user) from tools like [BuildMaster](https://inedo.com/buildmaster/) and OctopusDeploy, giving you a package-centered view of deployments.

This feature lets you know precisely which package versions have been deployed when, by whom, using what tool, and so on. This simplifies the auditing process by keeping clear records directly in ProGet. It also helps you find vulnerable servers fast [when a known vulnerability is identified](/docs/proget/compliance/vulnerabilities): The feature in ProGet shows all the servers running that specific package, letting you make quick updates. 

There are no restrictions on this feature in the Free edition of ProGet. Package deployment records can be found, once configured, in the "History" tab on the Package Overview page.

## Custom Records {#custom-records data-title="Custom Records"}

Consumers of any package type may customize the Feed API GET request with the following HTTP headers:

{.docs}

- **X-ProGet-Deployment-Application** - (Required) the application or tool doing the deployment e.g., NuGet, BuildMaster, Some Custom Tool
- **X-ProGet-Deployment-Description** - (Required) brief summary of deployment
- **X-ProGet-Deployment-Target** - (Required) string that identifies where the package was installed, typically the server name         
- **X-ProGet-Deployment-Url** - (Optional) URL that links to more information about the deployment
- **X-ProGet-Deployment-UserName** - (Optional) name of the user performing the deployment, defaults to authenticated user
- **X-ProGet-Deployment-Date** - (Optional) ISO 8601 UTC date of deployment, or current date if not supplied

## Package Deployment API {#package-deployment-api data-title="Package Deployment API"}

If you are using a tool other than BuildMaster or OctopusDeploy to deploy your packages, you can use the [Package Deployment API](/docs/proget/reference/api/package-deployment-api) to extend this feature in ProGet and add deployment records from other tools.
