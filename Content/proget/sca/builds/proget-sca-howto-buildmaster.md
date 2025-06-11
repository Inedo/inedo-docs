---
title: "HOWTO: SCA Integration with BuildMaster"
order: 3
---

By connecting your [BuildMaster](https://inedo.com/buildmaster/) instance to an existing ProGet instance, you can integrate ProGet's [Software Composite Analysis (SCA)](/docs/proget/sca/builds/proget-sca-ci) features. These will analyze the packages in your builds and raise issues. This will allow you to block deployment in the stages of your pipeline if issues exist in these packages. You can also allow the blocking of pre-release packages.

This guide explains how to configure your BuildMaster application to use ProGet SCA.

:::(info) (⭐ Prerequisites)
For this guide, you will need to have the following:
* [Application](/docs/buildmaster/modeling-your-applications/buildmaster-applications-concepts) created in BuildMaster
* [Build Script](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts) Created
* [Pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines) Created
:::

## Step 1: Connect to ProGet
To connect your BuildMaster instance to ProGet you will need to generate an [API key](/docs/proget/api/apikeys#creating-and-managing-api-keys?#creating-and-managing-api-keys) in ProGet first. 

In your ProGet instance, navigate to "Administration Overview" > "API Keys & Access Logs" under "Security & Authentication" and select "create api key". You will need at least "Manage SCA (Projects & Builds)" selected, although you may wish to have "Upload SBOM documents" selected if you intend to upload these.

![API Key](/resources/docs/proget-createapikey-buildmaster.png){height="" width="50%"}

Select "Create API Key". Then in your BuildMaster instance, navigate to "Administration" > "ProGet Integration" under "Security & Authentication", and select "Connect to ProGet". You will also be given the option to do this from the home page.

![API Key](/resources/docs/buildmaster-home-apikey.png){height="" width="50%"}

From here enter the "ProGet server URL" and your "API key".

![Enter API Key](/resources/docs/buildmaster-enterapikey-proget.png){height="" width="50%"}


## Step 2: Edit Build Script
Next, edit your Build script by navigating to "Settings" > "Scripts" and selecting your Build.

![Script Edit](/resources/docs/buildmaster-settings-scripts-select.png){height="" width="50%"}

From here, select the ProGet feed you will use as your "package source" for your application. In this example we will be using `::public-nuget`. You will also need to check "Scan for packages dependencies and record in ProGet". Then select "Save".

![Script Template](/resources/docs/buildmaster-buildscript-nugetfeed.png){height="" width="50%"}

Checking the "Scan for packages dependencies and record in ProGet" box will add pgscan to the Build Script. This will inspect built/compiled code, generate an [SBOM document](/docs/proget/sca/builds/proget-sca-sbom-importing-exporting), and publish it to ProGet. We can see this as part of the Build script by viewing it in OtterScript.

![Otter](/resources/docs/buildmaster-otterscript-buildscript.png){height="" width="50%"}

## Step 3: Create a Build
Now, create the build by navigating to "Builds" > "Create New Build" and selecting "Create Build".

![Create Build](/resources/docs/buildmaster-build-create-build.png){height="" width="50%"}

You can now view the packages in this build by selecting it under "Builds" > "View Builds" and selecting the "Packages & Dependencies" tab.

![View Packages](/resources/docs/buildmaster-build-viewpackages.png){height="" width="50%"}

## Step 2: Edit Pipeline
Next, edit your Pipeline to add requirements at different stages to prevent deployment when package issues exist. Navigate to "Settings" > "Pipelines" and select "edit".

![Edit Pipelines](/resources/docs/buildmaster-pipelines-edit.png){height="" width="50%"}

Scroll to the stages you wish to add requirements to and select "Add a stage requirement". Then select "Edit Packages/Dependencies".

![Edit Packages](/resources/docs/buildmaster-pipelines-editpackages.png){height="" width="50%"}

In this example, we are going to check "No open issues on Packages & Dependencies" at the testing stage and "No unstable (pre-release) dependencies" at the production stage.

![Checks](/resources/docs/buildmaster-pipelines-automatedchecks.png){height="" width="50%"}

After making your changes scroll back to the top and select "Commit Changes" to finalize the pipeline.

## Step 3: Deploy to Testing
We can see that the pipeline is now blocked as a license issue exists in one of our packages.

![Testing Blocked](/resources/docs/buildmaster-testing-blocked.png){height="" width="50%"}

To resolve this, navigate to the "Packages & Dependencies" tab and select "Resolve Issues in ProGet", which will direct you to the "Issues" tab in your application's "Project".

![Issues](/resources/docs/buildmaster-packages-issue-1.png){height="" width="50%"}

From here, select the issue and then select "Resolve".

![Resolve](/resources/docs/proget-issue-resolve.png){height="" width="50%"}

Navigating back to your BuildMaster instance, the pipeline is now unblocked. 

![Unblocked](/resources/docs/buildmaster-build-testing-deploy.png){height="" width="50%"}

Select "deploy" and then select "Deploy to Testing". 

![Deploy Build](/resources/docs/buildmaster-deploybuild.png){height="" width="50%"}

## Step 4: Deploy to Production
Now we see that the pipeline is now blocked as one of the dependencies is a prerelease package.

![Production Blocked](/resources/docs/buildmaster-production-blocked.png){height="" width="50%"}

You can resolve the issue by using an acceptable release package, but for the purpose of demonstration, we will force deployment at this stage.

## Step 5: (Optional) Force Deployment
On occasions, you may wish to deploy, despite issues such as licenses or prerelease packages. In this case, you have the option of forcing deployment. In your pipeline, select "force".

![Force Deploy](/resources/docs/buildmaster-deploy-force.png){height="" width="50%"}

Next, select "force" and then "Force Promotion" to deploy at the Production stage.

![Force Build](/resources/docs/buildmaster-forcebuild.png){height="" width="50%"}

Your application will now deploy.

![Deployed](/resources/docs/buildmaster-pipeline-final-deployed.png){height="" width="50%"}
