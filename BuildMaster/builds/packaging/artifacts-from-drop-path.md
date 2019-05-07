---
title: Create Artifact Via Drop-Path
subtitle: Capture a BuildMaster Artifact From a Drop Path
keywords: buildmaster, artifacts, drop-path, create-artifact
Pre-requisites: Buildmaster, InedoCore
---

BuildMaster gives you a lot of flexibility when it comes to creating and deploying artifacts. In some cases you might want to create an artifact for deployment that includes files placed in a drop-path. 

The `Create-Artifact` operation allows you to collect all of the files in a directory, compresses them in a zip file, and save it to the artifact library. 

### How to create artifacts from a network folder

For example, if your company uses a shared network folder as a drop-path for compiled source code you can use this feauture to pick up those files and create an artifact for deployment in subsequent operations.  

Example plan:

```
Create-Artifact HdarsVendorFiles
(
    From: \\fileshare1\vendorDrop\Hdars\$ReleaseNumber
);
```

### Using File Masks to Include/Exclude Files

You can also include or exclude files or file types using File Masks [KB #1119](https://inedo.com/support/kb/1119/wildcard-masking-in-inedo-prouducts). 

Example plan with File Masks:
```
Create-Artifact HdarsVendorFiles
(
    From: \\fileshare1\vendorDrop\Hdars\$ReleaseNumber,
    Include: *.dll,
    Exclude: *.csproj
);
```

### Additional Options for Create-Artifact

You can further customize this operation by specifying values for these additional options:

- Deployable name:  Associates the created artifact with a deployable in the application; this defaults the current deployable in context.
- Verbose logging: Using this will log every file within the artifact along with all activity that occurs during the creation of the artifact.  This is great for auditing or debug purposes, but it wiill take up additional log space.
- Ignore empty artifact: Checking this box will configure the Ignore Empty Artift setting, which, when checked, will ignore empty artifacts. 
- Overwrite:  This will overwrite existing artifacts. Re-running a plan execution will fail if the artifacts are found. Set this to true to bypass this default behaviour. 
- Use legacy storage path: For more information about legacy paths see: [#KB1117](https://inedo.com/support/kb/1117/upgrade-notes-for-buildmaster-v5#legacy-storage-paths) 
- Include system files: By default system files are excluded. Set this to true if you need to include all system files. 
- Include hidden files: By default hidden files are excluded. Set this to true if you need to include all files or are unsure if there are hidden files that you may need.


Example plan with additional Options:
```
Create-Artifact MyArtifact
(
    From: C:\Projects,
    Include: *.dll,
    Exclude: *.csproj,
    Deployable: Web,
    Verbose: false,
    IgnoreEmptyArtifact: true,
    Overwrite: false,
    UseLegacyStoragePath: true,
    IncludeSystemFiles: false,
    IncludeHiddenFiles: true
);
```

For more information and sample usage visit our [documentation](https://inedo.com/support/documentation/buildmaster/reference/operations/artifacts/create-artifact)