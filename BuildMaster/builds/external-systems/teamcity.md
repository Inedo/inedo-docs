---
title: Importing from TeamCity
subtitle: Import a TeamCity Artifact into BuildMaster
keywords: buildmaster, artifacts, teamcity, import
---

BuildMaster is a fully-functional Continuous Integration tool for a variety of platforms allowing your organization to start using CI/CD today. BuildMaster also supports referencing or importing artifacts from other CI tools like TeamCity.  

If your deployment engineers are using BuildMaster but another team is using TeamCity to build and create the artifacts, BuildMaster can effectively import that artifact to be utilized throughout the rest of the deployment process.

#### Implementation

BuildMaster can queue a TeamCity build and import the artifact with a few simple steps:

1. Ensure you have the TeamCity extension [installed](/den/inedox/teamcity) in the `Administration > Extensions` page
2. Create TeamCity credentials in the `Administration > Resource Credentials` page named "TeamCity" and use your TeamCity URL and login credentials.
3. Add the _Queue TeamCity Build_ operation into your build plan with the TeamCity project name and TeamCity resource credentials. This will queue a TeamCity build and wait for the task to complete before moving on to the next operation. This step is optional if the build and corresponding TeamCity artifact was already created:
```
TeamCity::Queue-Build
(
    Credentials: TeamCity,
    Project: HDars,
    BuildConfiguration: HdarsBuildConfig
);
```
4. Add the _Import Artifact from TeamCity_ operation into your build plan and fill in the operation's fields. Once the build queued in the previous operation completes, the specified TeamCity artifact will then be imported into BuildMaster:
```
TeamCity::Import-Artifact
(
    Credentials: TeamCity,
    Project: HDars,
    BuildConfiguration: HdarsBuildConfig,
    Artifact: HDars-Service
);
```

#### Next Steps
After you have imported your artifact from TeamCity, BuildMaster can now [deploy](/docs/buildmaster/reference/operations/artifacts/deploy-artifact) it to any number of servers or targets.

#### Self-Service TeamCity Builds

With [release templates](/docs/buildmaster/releases/templates), you can build an easy-to-use deployment pipeline using data directly from TeamCity. You can set up a template variable as a _dynamic list_ and use the TeamCity resource credentials as the source and select the build configurations directly from TeamCity in real-time. This saves time and gives you more control over what is being imported. Refer to the [Choosing a specific TeamCity Build with BuildMaster](https://inedo.com/support/tutorials/buildmaster/teamcity/choosing-specific-artifact-from-teamcity) tutorial for more information. 

#### Capturing TeamCity Build Numbers

Once you have queued a build and imported the artifact from TeamCity you can use the `$TeamCityBuildNumber` variable in conjunction with a [custom value renderer](/docs/buildmaster/administration/value-renderers). This is a convenient way you to quickly link back to the TeamCity server to get an overview of the build that was imported.
