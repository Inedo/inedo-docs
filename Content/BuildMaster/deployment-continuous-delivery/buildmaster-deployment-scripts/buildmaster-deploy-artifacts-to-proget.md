---
title: "Deploy as a ProGet Asset Script Template"
order: 5
hidden: true
---

A script template is a simple, no-code solution for performing common build or deployment operations, and can be converted to OtterScript at any time.

The Deploy Artifacts to File Share script template will:
* [Deploy artifact to Proget Asset Directory](#deploying-artifact)

This article will detail the options for these steps, as well as how they're performed.

:::(Info)
This script template requires a connection to a ProGet instance with an asset directory.
:::


## Using this Script Template

:::(Internal) (TODO: Fix Screenshot (sizing and border))
:::

To add this script template to your application, navigate to Scripts, click  "Add Script", and then select "Deploy as a ProGet asset" from the list of Build Script Templates. After doing so, you'll see a dialog like this:
![buildmaster-deploy-proget-asset-script-template](/resources/docs/buildmaster-deploy-proget-asset-script-template.png){height="" width="50%"}

There are a lot of options available, and this article will discuss how these options are used in different steps of the build process.

## Deploying Artifact
First, the script template will deploy the artfifact to the specified asset directory. Optionally, it can be deployed as a single zip file.

:::(Info) (🛠 OtterScript Notes)
The `Deploy-Artifact` operation will be run with a temporary path as the `To` parameter, and the `ProGet::Upload-Assets` operation will be used to upload the artifact to the ProGet asset directory.
:::
