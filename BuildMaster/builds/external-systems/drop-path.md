---
title: Importing from a Drop Path
subtitle: Importing an Artifact from a Drop Path
keywords: builds, artifacts, drop-path
sequence: 500
---

BuildMaster gives you a lot of flexibility when it comes to creating and deploying artifacts. In some cases you might want to create an artifact for deployment that includes files that were placed in a drop path.

The Create-Artifact operation allows you to collect all of the files in a directory, compresses them in a zip file, and save it to the artifact library with the intention of deploying it later.

## Use-Cases

While there are several ways to utilize a drop path, the two most common drop path use-cases in BuildMaster are described as follows:

1. all build output will be exported to a specific subdirectory of a base drop path using the release number and the user or tool that does this will notify a BuildMaster user (or automate the call to BuildMaster) instructing the user or system to capture the contents into an artifact
2. build output is copied in a predetermined drop path, a BuildMaster user (or the system via API call) causes all files in this directory to be captured into an artifact and associated with a build

### Configuring the Build Plan {#build-plan}

Creating a plan to handle artifact capture from a drop path is as simple as using the `Create-Artifact` operation. For example, if your company uses a shared network folder as a drop-path for compiled source code you can use this feature to pick up those files and create an artifact for deployment in subsequent operations.

Example plan (use-case #1):

```
Create-Artifact HdarsVendorFiles
(
    From: \\fileshare1\vendorDrop\Hdars\$ReleaseNumber
);
```

Example plan (use-case #2):

```
Create-Artifact BuildOutput
(
    From: \\fileshare1\buildOutput
);
```

## Examples {#examples}

The two use-cases for this pattern are demonstrated on our public BuildMaster instance at the following locations:

 - Using a `$ReleaseNumber` subdirectory: https://buildmaster.inedo.com/applications/41/plans
 - Capturing from a predetermined single drop path: https://buildmaster.inedo.com/applications/42/plans

## Configuring Automatic Build Triggering {#build-triggering}

Starting in <span class="upcoming">BuildMaster v6.1.10</span>, you can configure a scheduled [Repository Monitor](/support/documentation/buildmaster/builds/continuous-integration/repository-monitors), which will routinely poll an existing drop path to check if there is any new content within it that BuildMaster needs to capture. 