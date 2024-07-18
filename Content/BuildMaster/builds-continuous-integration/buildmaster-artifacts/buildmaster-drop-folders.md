---
title: "Importing from a Drop Folder"
order: 1
---


BuildMaster can be configured to capture build output or build artifacts from a drop folder and store them until they are ready for deployment. Using drop folders is a common practice when build and release teams are organizationally separated.

It is also a good way to start incremental automation.

## Modeling the Import Process: Manual Import

In many cases, build engineers or CI systems will notify release teams once an build is copied into a drop folder. The BuildMaster model for this process is simple and requires minimal setup:

 - a known location to act as the drop folder—a path on disk or network accessible to the BuildMaster service or agent
 - basic implementation in BuildMaster (outlined below)

In the following example, BuildMaster assumes that the build output will be located in a subfolder of the root drop folder, and that the name of the subfolder matches the release number within BuildMaster, for example: 

```
\\filesv.corp.local\HdarsDropFolder\Releases\2.4.1
```

You should capture a build artifact from this folder, as this ensures that you deploy the same set of files each time you deploy this release.

To capture the build output from this folder, you can use a `Create-Artifact` operation:

```
Create-Artifact HdarsVendorFiles
(
    From: \\filesv.corp.local\HdarsDropFolder\Releases\$ReleaseNumber
);
```

Once captured, you can deploy to servers or the cloud in subsequent pipeline stages:

```
Deploy-Artifact HdarsVendorFiles
(
    To: D:\Websites\HdarsSite
);
```

### BuildMaster Application Template

::: (info)
To generate the configuration for this pattern in your own instance of BuildMaster, simply create a new application using the Drop Folder Import template.
:::

The Drop Folder Import application template will automatically create the following relevant items:

 - Import [plan](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts) - a script that captures the build output from the drop folder
 - Release [pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines) - a pipeline that uses the import plan in the first stage
 - `$DropFolder` [configuration variable](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables) - referenced in the import plan as the root folder for the build output

Once the application is generated in BuildMaster, creating a new build will capture all files in `$DropFolder\$ReleaseNumber` into a [build artifact](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts) that can be deployed to future pipeline stages.

## Modeling the Import Process: Drop Folder Monitoring

Importing from a drop folder can be automated a step further by configuring a [build trigger](/docs/buildmaster/administration/buildmaster-resource-monitors). A build trigger runs periodically and, based on the existence of a special marker file, imports the build output as a build artifact into BuildMaster. Once imported, the files are deleted from the drop folder.

Compared to manual import into the drop folder, this pattern is more suitable for automated processes that copy files into the drop folder, such as an existing CI system or a vendor that creates builds on a regular basis.

### BuildMaster Application Template

::: (info) 
To generate the configuration for this pattern in your own instance of BuildMaster, simply create a new application using the Drop Folder Monitor template.
:::

The Drop Folder Monitor application template will automatically create the following relevant items:

 - Monitor [plan](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts) - a script that runs periodically, and if `ready.txt` exists in the `$DropFolder`, the import plan is run
 - Import [plan](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts) - a script that captures the build output from the drop folder
 - Release [pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines) - a pipeline that uses the import plan in the first stage
 - `$DropFolder` &nbsp;[configuration variable](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables) - referenced in the import plan as the root folder for the build output

Once the application is generated, BuildMaster will continuously monitor `$DropFolder` for a file named `ready.txt` and when found, will create a new build to capture all other files in `$DropFolder` into a [build artifact](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts) that can be deployed to future pipeline stages. After the files are captured, they are deleted from the drop folder to prepare for the next time files are dropped into it.

#### The Marker File: `ready.txt`

Since BuildMaster periodically scans a drop folder with any files in it, there is no way to determine whether or not that folder is filled with all the files that should be part of the artifact. The presence of a marker file is a simple way to signal to BuildMaster that the contents of the folder are correct and complete. Of course, this file should only be added as a final build task or by human intervention.

## Customizing the Process

The above patterns and templates are designed to help you get started with drop folder automation and can be customized to meet the needs of your organization. Some ideas for customization include:

 - using the `@FileMask` function to iterate directories and extract a release number
 - omit searching for `ready.txt` and just assume the drop path files are correct and will not experience a race condition (e.g. all files are copied to `\drop-folder-tmp` and the directory is renamed to `\drop-folder` when all files are present)
 - using `$FileContents` to read the contents of a text file in order to make automation decisions
 - using the `Create-Build` and `Create-Release` operations in the monitor plan to create builds or releases for other applications in BuildMaster