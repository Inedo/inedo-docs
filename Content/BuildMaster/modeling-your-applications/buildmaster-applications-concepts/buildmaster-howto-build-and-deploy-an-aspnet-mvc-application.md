---
title: "HOWTO: Build and Deploy an ASP.NET MVC Application (CI/CD)"
order: 1
---

BuildMaster can [build](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts) and [deploy](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts) a multitude of applications to various environments, each with their own specific settings.

In this guide we will look at creating an ASP.NET CORE application called ["vatcompweb"](https://gitlab.com/inedo-samples/vatcompweb) in BuildMaster from code hosted in a GitLab repository. We will then look at how we can deploy it to IIS in Integration, Testing and Production environments in a pipeline, which determines where the build will be deployed, as well as what approvals are required before deploying to each stage.

:::(Info) (📺 3-minute Video:  Getting Started with BuildMaster)
<iframe width="600" height="337" src="https://www.youtube.com/embed/Byi7gnOn6Hc?si=1EXwRRGjhsvvt-9T" frameborder="0" allowfullscreen="true"></iframe>
:::

## Step 1: Create Application

An [Application](/docs/buildmaster/modeling-your-applications/buildmaster-applications-concepts) in Buildmaster is connected to a source like a Git Repository and is used to build and/or deploy a web application or other project. 

Start by creating a new application by navigating to "Applications" and selecting "Create Application".

![Create Application](/resources/docs/buildmaster-newapp-createapp.png){height="" width="50%"}

Next select the source of the application. BuildMaster will offer several sources, the most common being [Git Repositories](/docs/buildmaster/builds-continuous-integration/buildmaster-git-source-control) and CI Tools such as [Jenkins](/docs/buildmaster/tools-service-integrations/buildmaster-integrations-jenkins) and [TeamCity](/docs/buildmaster/tools-service-integrations/buildmaster-integrations-teamcity). As we are connecting to GitLab, select "Git Repository".

![Select Source](/resources/docs/buildmaster-newapp-source.png){height="" width="50%"}

Now select the Source Control. BuildMaster will offer a range of sources for your application, including the option to select a generic Git repository. Each choice will guide you through the specific options to connect to your chosen source control. In this case, select "GitLab" as this is the service that hosts your application repository.

![Select GitLab](/resources/docs/buildmaster-gitrepo-gitlab.png){height="" width="50%"}

From here enter your Gitlab user name and Personal Access Token [generated from your Gitlab profile](/docs/buildmaster/tools-service-integrations/buildmaster-integrations-gitlab#generating-a-gitlab-personal-access-token). Then select "Select GitLab Repository".

![Enter Credentials](/resources/docs/buildmaster-gitrepo-credentials.png){height="" width="50%"}

Next select the Group and Repository of your application on GitLab. Then select "Set Application Name". In this case our application group is `inedo-test-1` and our application is `vatcompweb`.

![Repo Connect](/resources/docs/buildmaster-gitrepo-connect.png){height="" width="50%"}

Select a name for your application. You can also choose to enter a description of your application. We are keeping the name the same as our application and calling it `vatcompweb`. Once you have entered the application name, select "Create Application".

![Naming](/resources/docs/buildmaster-gitrepo-name.png){height="" width="50%"}

For ease of setup, BuildMaster will automatically detect suitable development platforms based on the application you have created. 

In this case BuildMaster has detected that our application was written in ASP.NET and offers ".NET Application" as a choice. If your development platform is not listed, you can select "Show All" to display the full list. In this case, select ".NET Application".

![App Type](/resources/docs/buildmaster-gitrepo-apptype.png){height="" width="50%"}

Buildmaster will now allow you to specify if you are using a [Docker Container](/docs/buildmaster/docker?highlight=docker) image. In this guide we are not using a Docker image, so select "No, I am not using Docker".

![Docker](/resources/docs/buildmaster-app-docker.png){height="" width="50%"}

Finally select how you will deploy your application. This will only impact BuildMaster's set-up guidance and recommended selections, and you can add as many deployment targets to an application as needed.

However, for our application we will deploy our application to IIS, so select "Windows Server".

![Deployment](/resources/docs/buildmaster-app-deploy.png){height="" width="50%"}

Your application has now been created. BuildMaster will guide you throughout the set up, and now suggests that you create a build script next.

![Created](/resources/docs/buildmaster-app-created.png){height="" width="50%"}

## Step 2: Create Build

To create a build script. Navigate to "Settings" > "Scripts" and select "Add Script".

![Build Script](/resources/docs/buildmaster-script-build.png){height="" width="50%"}

BuildMaster will offer a range of script templates. Again, BuildMaster will detect your application and offer recommended templates to use. As our application is in ASP.NET, select "Build .NET Project".

![Select Build Script](/resources/docs/buildmaster-script-select-build.png){height="" width="50%"}

From here, first select your applications solution ".csproj" file.

Then, you'll need to select your Nuget Package Source. We recommend [setting up a NuGet feed in ProGet](/docs/proget/overview#step-2-create-your-first-feed-in-proget?highlight=getting%20started%20with%20proget#step-2-create-your-first-feed-in-proget) and using that to manage the packages you use, For this example we have created one previously, that we named “public-nuget”, and will select that. For more information we recommend reading [Packages and Dependencies](/docs/buildmaster/builds-continuous-integration/buildmaster-build-packages-dependencies?highlight=packages%20and%20dependencies) to learn more.

For "Build Configuration" select "Release" as we are creating a release build. 

BuildMaster allows us to set dotnet specific options, so select the "dotnet options" tab. 

![dotnet Options](/resources/docs/buildmaster-buildscript-create-dotnet.png){height="" width="50%"}

Here You can specify "dotnet" settings based on your preference. BuildMaster allows you to edit your script using the built in OtterScript editor. You can read ["Build .NET Project Script Template" here](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts/buildmaster-build-net-project-script-template) to learn more. For now, select "Save” to create the script. 

![dotnet Options](/resources/docs/buildmaster-script-build-dotnet.png){height="" width="50%"}

Your build script has now been created. Now navigate to "Build", select "Create New Build" and select "Create Build" in the following modal.

![Build Build](/resources/docs/buildmaster-build-createbuild.png){height="" width="50%"}

BuildMaster will then build your application.

![Build Complete](/resources/docs/buildmaster-build-complete.png){height="" width="50%"}

## Step 3: Inspect Build Artifacts

In BuildMaster, [Build Artifacts](/docs/buildmaster/builds-continuous-integration/buildmaster-artifacts) are the files added to the build after building/compiling your code that you intend to deploy/release.

The next step will be to inspect and confirm them. Start by selecting the files link under the "Build Overview". From here, confirm the artifacts listed on the page by selecting "Artifacts look Good, Continue to Next Step", in which we will create a configuration file to support our application deployment.

![Artifacts](/resources/docs/buildmaster-artifacts-list.png){height="" width="50%"}

## Step 4: Create Configuration File

[Configuration Files](/docs/buildmaster/deployment-continuous-delivery/buildmaster-applications-configuration-files) allow you set and deploy environment-specific configuration values without the need for code changes, manually logging into servers, or deploying the entire application.

To create one navigate to "Settings" > "Configuration Files" and then select "Create Application Configuration File". BuildMaster provides several configuration options. To view these, select "All Options".

![Add Config File](/resources/docs/buildmaster-configurationfiles-add-alloptions.png){height="" width="50%"}

These options include the ability create a blank configuration file, instances that use key/value pairs, and the configuration format used in previous versions of BuildMaster.

Select "Import File from Git Repository (Recommended)" as we want to create multiple instances such as Integration, Test and Production in our deployment.

![Config Options](/resources/docs/buildmaster-configurationfiles-options.png){height="" width="50%"}

Select `appsettings.json` as your configuration file, and select "Specify Instances". BuildMaster will pre-specify the Integration, Testing and Production instances we need, so select "Set File Options".

![Add Config File](/resources/docs/buildmaster-configurationfiles-instances.png){height="" width="50%"}

We will now set several security and deployment options. The "General" Tab will allow us to specify the file name and create a description.

![General Tab](/resources/docs/buildmaster-configurationfiles-general.png){height="" width="50%"}

The "Instance Security" tab allows us to ensure the security of our environment by controlling who can view, edit and deploy our instances through restricting specific actions to specific instances. 

![Instance Security Tab](/resources/docs/buildmaster-configurationfiles-security.png){height="" width="50%"}

You can also specify deployment options which are covered in more detail on our [Configuration Files](/docs/buildmaster/deployment-continuous-delivery/buildmaster-applications-configuration-files#creating-an-application-configuration-file?#creating-an-application-configuration-file) page. For now though, select "Create Configuration File".

## Step 5: Create Deploy Script

A [deploy script](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts) in BuildMaster is used to deploy artifacts or packages that are created with a build script, which will be run during the several stages of the  pipeline we will create later in this guide.

To create a deploy script, navigate to "Settings" > "Scripts" and select "Add Script".

![Deploy Script](/resources/docs/buildmaster-script-deploy.png){height="" width="50%"}

BuildMaster will recommend creating a Deploy IIS Site script, as we specified earlier, so select "Deploy IIS Site".

![Deploy Script](/resources/docs/buildmaster-script-select-deploy.png){height="" width="50%"}

Here we will configure our script. We will call the App Pool Name `VatCompWebAppPool` and set the deploy path as `C:\Websites\VatCompWeb`, leaving the other options as-is. After entering these details, select "Save".

![Deploy Config](/resources/docs/buildmaster-script-deploy-config.png){height="" width="50%"}

Now, deploy your application by navigating to "Builds" > "View Builds", selecting your build from the list

![Deploy Config](/resources/docs/buildmaster-deploy-select.png){height="" width="50%"}

Then select "Deploy Build" on the build's overview page, and select "Run Deploy Script".

![Deploy Button](/resources/docs/buildmaster-deploy-button.png){height="" width="50%"}

![Deploy Window](/resources/docs/buildmaster-deploy-window.png){height="" width="50%"}

And finally select your server you named earlier and select "Deploy Build". This will deploy our application.

![Deploy Window](/resources/docs/buildmaster-deploy-server.png){height="" width="50%"}

## Step 6: Create Pipeline

Now we will create a [Pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines). In Buildmaster, pipelines are the servers and environments that your builds will be deployed to formed into repeatable release processes, including the manual and automatic approvals required at each stage of the process.

To create your pipeline, navigate to "Settings" > "Pipelines" and select "Create Pipeline".

![Select Pipeline](/resources/docs/buildmaster-pipelines-select.png){height="" width="50%"}

BuildMaster will once again offer options immediately relevant to our environment, with more available by selecting "Show all..". Select "Main/Release Branch" as we are creating a single, main pipeline for a release.

You will be redirected to your application's release page. From here select "Create Pipeline", which will create your pipeline.

![New Pipeline](/resources/docs/buildmaster-pipelines-new.png){height="" width="50%"}

![Create Pipeline](/resources/docs/buildmaster-pipelines-create.png){height="" width="50%"}

## Step 7: Create Release

Next we will need to create a [Release](/docs/buildmaster/modeling-your-applications/buildmaster-releases). This is used to model and visualize a software release, that is, to create, test, and deliver code changes to a production environment in a consistent process, grouping related builds together under a common release number and deployment pipeline.

To create a release, navigate to "Release" > "Create Release". Specify the release "Branch", "Pipeline", "Release Number" and "Release Name", and then select "Create Release".

![Create Release](/resources/docs/buildmaster-release-new.png){height="" width="50%"}

Your application is now set up, complete with the build stages ready in the pipeline you created.

![Create Release](/resources/docs/buildmaster-application-done.png){height="" width="50%"}

## Step 8: Add Issue Tracker

Finally, we will add an [Issue Tracker](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking) to our application. This will automatically pull issues associated with our application, allowing us to see release notes, or prevent deployment if there are open issues. 

To add an issue tracker, navigate to "Issues" and select "Connect to an Issue Tracker".

![Add Issue Tracker](/resources/docs/buildmaster-issuetracker-new.png){height="" width="50%"}

Now select the GitLab account that you connected to earlier, in this case we select "Issues in Inedo-test-1/vatcompweb".

![Connect](/resources/docs/buildmaster-addissue-gitlab.png){height="" width="50%"}

Release mapping will make sure GitLab Issue's "Milestones" field and the BuildMaster Release Number/Name are the same, to map BuildMaster's releases to GitLab versions. Keep the settings the same, and select "Save & View Issues".

![Release Mapping](/resources/docs/buildmaster-issuetracker-releasemapping.png){height="" width="50%"}

You will now be redirected to the Issues page. If there are any current issues, they will be displayed here.

![Issues](/resources/docs/buildmaster-issues-list.png){height="" width="50%"}