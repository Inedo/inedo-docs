---
title: Importing from a Drop Folder
subtitle: Importing an Artifact from a Drop Folder
keywords: builds, artifacts, drop-folder
sequence: 500
---

BuildMaster can be configured to capture build output or build artifacts from a drop folder and store them until they are ready for deployment. Using drop folders is a common practice when build and release teams are separated organizationally.

## Modeling the Import Process: Manual Import

In many cases, build engineers or CI systems will notify release teams once an build is copied into a drop folder. The BuildMaster model for this process is simple and requires minimal setup:

{.docs}
 - a known location to act as the drop folder - a path on disk or network accessible to the BuildMaster service or agent
 - basic implementation in BuildMaster (outlined below)

In the following example below, BuildMaster assumes that the build output will be located in a subfolder of the root drop folder, and the name of the subfolder matches the release number within BuildMaster, for example: 

```
\\filesv.corp.local\HdarsDropFolder\Releases\2.4.1
```

### BuildMaster Application Example

::: {.attention .best-practice}
To generate the configuration for this pattern in your own instance of BuildMaster, simply create a new application using the "Drop Folder Import" template.
:::

The "Drop Folder Import" application template will automatically create the following relevant items:

{.docs}
 - Import [plan](/docs/buildmaster/deployments/plans) - a script that captures the build output from the drop folder
 - Release pipeline - a [pipeline](/docs/buildmaster/verification/pipelines) that uses the import plan in the first stage
 - `$DropFolder` [configuration variable](/docs/buildmaster/administration/configuration-variables) - referenced in the import plan as the root folder for the build output

Once the application is generated in BuildMaster, creating a new build will capture all files in `$DropFolder\$ReleaseNumber` into a [build artifact](/docs/buildmaster/builds/packaging/artifacts) that can be deployed to future pipeline stages.

## Modeling the Import Process: Drop Folder Monitoring

Importing from a drop folder can be automated a step further by configuring a [build trigger](/docs/buildmaster/builds/continuous-integration/build-triggers-and-monitors). A build trigger runs periodically and, based on the existence of a special marker file, imports the build output as a build artifact in BuildMaster.

### BuildMaster Application Example

::: {.attention .best-practice}
To generate the configuration for this pattern in your own instance of BuildMaster, simply create a new application using the "Drop Folder Monitor" template.
:::

The "Drop Folder Monitor" application template will automatically create the following relevant items:

{.docs}
 - Monitor [plan](/docs/buildmaster/deployments/plans) - a script that runs periodically, and if `ready.txt` exists in the `$DropFolder`, the import plan is run
 - Import [plan](/docs/buildmaster/deployments/plans) - a script that captures the build output from the drop folder
 - Release pipeline - a [pipeline](/docs/buildmaster/verification/pipelines) that uses the import plan in the first stage
 - `$DropFolder` [configuration variable](/docs/buildmaster/administration/configuration-variables) - referenced in the import plan as the root folder for the build output

Once the application is generated, BuildMaster will continuously monitor `$DropFolder` for a file named `ready.txt`, and when found, will create a new build to capture all other files in `$DropFolder` into a [build artifact](/docs/buildmaster/builds/packaging/artifacts) that can be deployed to future pipeline stages. After the files are captured, they are deleted from the drop folder to prepare for the next time files are dropped into it.