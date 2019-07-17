---
title: Import From Jenkins
subtitle: Import Artifact  From Jenkins
keywords: buildmaster, artifacts, jenkins, import
pre-requisites: Buildmaster, InedoCore, Jenkins
---


BuildMaster is a fully-functional Continuous Integration tool for a variety of platforms allowing your organization to start using CI/CD today. BuildMaster also supports referencing or importing artifacts from other CI tools like Jenkins.  

If your deployment engineers are using BuildMaster but another team is using Jenkins to build and create the artifacts, BuildMaster can effectively import that artifact to be utilized throughout the rest of the deployment process. 

#### Implementation

BuildMaster will easily **queue** a Jenkins build and **import** the artifact with a few simple steps.

1. Ensure you have the Jenkins Extension installed in the /adminsitration/extensions page.
2. Create Jenkins credentials in the /administration/credentials page named `Jenkins` and use your Jenkins Url and login credentials (or access token). 
3. Add the _Queue Jenkins Build_ operation into your build plan with the Jenkins Job Name and your Jenkins credentials. This will queue a Jenkins build and wait for the task to complete before moving on to the next operation. 
```
Jenkins::Queue-Build
(
    Credentials: Jenkins,
    Job: $jenkinsJob      
);
```
4. Add the _Import Jenkins Artifact_ operation into your build plan and complete the form. Once the queued build completes buildmaster will import the artifact from Jenkins.    
```
Jenkins::Import-Artifact
(
    Credentials: Jenkins,
    Job: $JenkinsJob,
    Artifact: test-artifact
);
```

#### Additional Jenkins Options
- Job name:  The Name of the Job you are targeting from Jenkins. Typically this would be a build variable. 
- Build number: The build number may be a specific build number, or a special value such as `lastSuccessfulBuild`, `lastStableBuild`, `lastBuild`, or `lastCompletedBuild`.  This the best way to target the exact build you want to import from Jenkins.
- Artifact name:  The name of the artifact in BuildMaster once it is captured from the `{jenkinsUrl}/job/{jobName}/{buildNumber}/artifact/*zip*/archive.zip` endpoint.
- Set build number to variable:  You can set any variable to the build number value returned by Jenkins. Default it `$JenkinsBuildNumber`

#### Further Integration
Another recommended usage for the Jenkins extension is to incorporate Jenkins with our [release templates](https://inedo.com/docs/buildmaster/releases/templates). You can set up a template variable as a _dynamic list_ by using your Jenkins connection as the source. Then you'll be able to select the exact build directly from Jenkins in real-time. This saves time and gives you more control over what is being imported. [See the full documentation](https://inedo.com/support/tutorials/buildmaster/jenkins/choosing-specific-artifact-from-jenkins). 

#### Next Steps
After you have imported your artifact from Jenkins, BuildMaster can now [deploy](https://inedo.com/docs/buildmaster/reference/operations/artifacts/deploy-artifact) it to any number of servers or targets. 

#### Reference back to Jenkins
Once you have queued a build and imported the artifact from Jenkins you can use the `$JenkinsBuildNumber` variable to link directly back to jenkins with a [custom value renderer](https://inedo.com/docs/buildmaster/administration/value-renderers). This is a convenient way you to quickly browse to Jenkins to review the build that was imported. 
