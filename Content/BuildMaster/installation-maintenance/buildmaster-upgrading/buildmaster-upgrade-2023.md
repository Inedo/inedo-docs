---
title: "Upgrading to BuildMaster 2023"
nav-title: "BuildMaster 2023"
order: 2
url-slug: "buildmaster-upgrade-2023"
---

:::(Error) (Skip BuildMaster 2023: Upgrade Directly to BuildMaster 2024)
If you are using BuildMaster 6.1, BuildMaster 6.2, BuildMaster 7.0, or BuildMaster 2022 we recommend directly [Upgrading to BuildMaster 2024](/docs/buildmaster-upgrade-2024). If you using an earlier version, you will first need to [Upgrade to BuildMaster 6.1](/docs/buildmaster-upgrading-to-6-1).
:::


BuildMaster 2023 is a major update, and this article provides information about what will change, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade
BuildMaster 2023 is a major upgrade, and many of the changes were additive features, platform updates (library upgrades, etc.), and UI changes.

### Upgrading from BuildMaster 2022
:::(Info)
Upgrading from BuildMaster 2022 should be relatively easy
:::

No major features have been removed and there were no significant changes to the core components; we anticipate the upgrade will be relatively easy and no preparation will be required. 

You can safely downgrade to BuildMaster 2022 without rolling back the database, but new data, such as Docker- and Cloud-related resources may not work in the older version.

### Upgrading from BuildMaster 7.0 and BuildMaster 6.2

If you're currently using BuildMaster 6.2 or BuildMaster 7.0, we recommend directly upgrading to BuildMaster 2023. While it won't *hurt* to do incremental upgrades, there's almost never a benefit.

Please read the upgrade notes from each version to learn what changed and how to mitigate risks. To summarize:

In [BuildMaster 2022](/docs/buildmaster-upgrade-2022),we reworked Git integration, made pipeline changes, and PowerShell operation changes. However, there were very few issues reported - and they've been resolved in the latest versions.
    
In [BuildMaster 7.0](/docs/buildmaster-upgrading-to-v7), there was very little that changed from an upgrade standpoint. We mostly standardized libraries, adding Linux support, high-availability, etc.


### Upgrading from BuildMaster 6.1 or Earlier
:::(Warning)
If you're upgrading from BuildMaster 6.1 or earlier, upgrading to BuildMaster 2023 may be challenging.
:::
In BuildMaster 6.2, all [Legacy Features](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features) were removed from the product. See [Upgrading to BuildMaster 6.2](/docs/buildmaster-upgrading-to-6-2) to learn more about what else changed in that release. Alternatively, you may want to consider an [application-by-application migration approach](/docs/buildmaster/installation-maintenance/buildmaster-migrating-instance-to-new-server#migrating-applicationbyapplication).

You wont be able to upgrade to BuildMaster 2022 until the [Legacy Feature Detector](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) has reported no legacy features. This is only available in the latest versions of BuildMaster 6.1, which means you will need to [upgrade to BuildMaster 6.1](/docs/buildmaster-upgrading-to-6-1) first.

Because upgrading from BuildMaster 6.1 is more involved than previous upgrades, special support for it has been included with paid licenses. Simply use the [Submit Ticket Form](https://my.inedo.com/tickets/new), put `2023 UPGRADE` in the "How can we help" section, and include the [Legacy Features Dashboard Report Logs](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-features#legacy-feature-detector) in the text body of the ticket.

## New Feature: First-Class Docker Integration
:::(Info)
This is available as a preview feature in BuildMaster 2022.13, and we do not anticipate substantial changes in BuildMaster 2023.
:::

Although BuildMaster has long supported container-based development with the `Docker` extension, we made Docker a core part of BuildMaster 2023's functionality, in a similar manner to how Git is integrated.

![buildmaster-docker-new-app-prompt](/resources/docs/buildmaster-docker-new-app-prompt.png){height="" width="50%"}

This feature leverages existing components within BuildMaster, and provides specialized views to help you [Containzerizing your Application](/docs/buildmaster/docker/containerizing-applications) and [Deploy With Docker Run Config](/docs/buildmaster/docker/run-config). 

#### New: Run Build Scripts in Containers
Instead of configuring build servers to run your builds, you can use pre-built containers like `microsoft-dotnet-sdk` and `maven` that have the tools and components already installed, or customerize those containers as well. 

When [Image-based Services](/docs/buildmaster/administration/buildmaster-image-based-services) is enabled, script templates like [Build .NET Project](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts/buildmaster-build-net-project-script-template) and [Build Maven Project](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts/buildmaster-build-maven-project-script-template) will display a "Docker" tab that lets you select the container to run your build script in.

#### Changes: Docker Resources/Credentials
In previous versions, you could create a "Docker Container Registry" resource and specify
* Credentials used to authenticate to the registry
* Optional registry prefix like `proget.kramerica.local:443/mycontainers`

And then, in Docker-based operations, you could specify a "short" repository name like `myapp`, which would then be combined into the full repository name of `proget.kramerica.local:443/mycontainers/myapp`. The operations would use `docker login` to authenticate using the specified credentials.

The Credentials were typically a Username/Password credential, as that's what `docker login` uses. An API token was also supported, but the operations would simply use `api` as the username, and then the API token as the password. This generally only with ProGet we believe.

In BuildMaster 2023, "Docker Container Registry" resources will be transparently converted to a "Generic Docker Repository" resource. The "prefix" and "short name" will continue to function, but is considered deprecated in BuildMaster 2023.

API Token credentials will still work, but they will "disappear" if you edit the if you edit the resource. You'll need to create a Username/Password, or switch to a different repository type.

You will also be able to add a "ProGet Container Repository", which will allow you to select a feed and then enter a repository name without worrying about credentials. Down the line, you'll be able to add other service-specific resources like "Azure Container Repository".

#### Changes: Docker Operations
All of the Docker-based operations were modified to support the Resource/Credential changes. In addition, `Docker::Assemble-Image` was deprecated in favor of simply maintaining your Dockerfile in BuildMaster.

## New Feature: First-Class npm Integration
:::(Info)
This is available as a preview feature in BuildMaster 2022.12, and we do not anticipate substantial changes in BuildMaster 2023.
:::

If your web application uses `npm` to build or enhance their front-end pages, there is now an option to enable building an npm project on the "npm Options" tab of script templates like [Build .NET Project](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts/buildmaster-build-net-project-script-template) and [Build Maven Project](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts/buildmaster-build-maven-project-script-template).

Once enabled,  the script template will set an npm version (optional), install npm packages (`npm install`), and run the build script (`npm run build`). You'll be able to select an npm feed from a connected PRoGet instance, and publish those dependencies as a SCA Project Release in ProGet.

This feature is powered by the newly-created [Node integration (extension)](/docs/buildmaster/development-platforms/buildmaster-platforms-node-js), which is also included with BuildMaster.


## New Feature: First-Class Azure App Services Integration
:::(Info)
This is available as a preview feature in BuildMaster 2022.9, and we do not anticipate substantial changes in BuildMaster 2023.
:::

It's now much easier to deploy your applications to [Azure App Services](/docs/buildmaster/deployment-targets/azure/azure) with a Built-in Azure Connection Wizard and Script Templates/Operations that simplify working with Slotted Deployments.


BuildMaster 2022.9 and later includes built-in operations for deploying your applications to an Azure Web App.  For other Azure operations, please see our [deployment using az.exe](/docs/buildmaster/deployment-targets/azure/az-exe) documentation.

## Redesigned Feature: Issue Tracking Integration
BuildMaster's [Issue Tracker Integration](/docs/buildmaster/modeling-your-applications/buildmaster-applications-issue-tracking) had a major overhaul in BuildMaster 2023. Functionally, it's works the same, and BuildMaster will still:
* Connect to issue trackers like Jira, Azure DevOps, GitHub, YouTrack, etc.
* Pull issues associated with active releases
* Quickly see release notes (and generate them)
* Block deployment if there are open issues
* Automatically update issue statues after deployment

However, BuildMaster 2023 makes it much easier to configure these connections and improves the UI quite a bit: 
![buildmaster-issues-on-release](/resources/docs/buildmaster-issues-on-release.png){height="" width="75%"}

Behind the scenes, the integration uses a new model as well: an "Issue Tracker Project" (Secure Resource) is used in conjunction with an "Issue Tracker Service" (Secure Credential). The Issue Tracker Project resource is what's used to pull issues and update statuses.

#### Issue Sources Are Deprecated
BuildMaster 2022 and earlier used a different integration model: an "Issue Source" (now deprecated) pulled issues by connecting to an "Issue Tracker Service" (Secure Resource) using an "Issue Tracker Account" (Secure Credential). Issue Sources are not project-specific, and you could technically configure an Issue Source to configure to different projects using variables.

Unfortunately, this model is incompatible with the previous model, and there was no easy way to migrate connections. While Issue Sources will still work in BuildMaster 2023, but they are considered a legacy feature and will be removed in BuildMaster 2025. We recommend deleting your configured issue sources, and re-adding the issue tracker connections instead.

:::(Warning) (Issue Tracker Changes Risk Mitigation)
Although this feature was overhauled, this is a relavitely low-risk change. Issue tracking is not a core feature, and builds/deployments will function without it. 
:::

## OtterScript Updates
To help simplify your OtterScript scripts, we added a handful of control statements:
* `break` can be used in side of an iteration (loop) statement, and will exit the iteration
* `continue` can be used inside of an iteration (loop), and will skip to the next item
* `return` will exit  the current script or module
* `else if`, which will make long if/else statements easier to work with.

We also made some minor UI changes to the low-code Script Editor that makes it easier to work in visual mode.

### New "Set" Statement Behavior
The set statement (i.e. `set $myvar = myval;`) has a slightly new behavior; it will simply default to using the global scope when creating a new variable instead of the current "kind of local, kind of global" behavior.

For example, consider the following script.
```
# This script works in BuildMaster 2023 but fails in BuildMaster 2022
{
  set $myvar = myval;
}
Log-Information "myvar" is $myvar;
```

In BuildMaster 2022 and earlier, this would fail with an error like `$myvar is not declared`. But if you used `set global $myvar = newval`  then it would work and log the result. In BuildMaster 2023, the script will not error, and will simply work as if you wrote `set global`.

```
# This script works in BuildMaster 2022 and BuildMaster 2023
{
  set global $myvar = myval;
}
Log-Information "myvar" is $myvar;
```

The above example works, but this is not a good general purpose solution, because `set global` *only* considers the global scope for assignment, so the below example could yield confusing behavior:
```
{
  set $myvar = myval;
  
  {
    set global $myvar = my new val;
  }

  Log-Information "myvar" is $myvar;
}
```
The output of the above example would be `myvar is myval`, because `set global` will _only_ assign or create variables in the global scope.

If you want BuildMaster 2023 to create a variable in the local scope, then you can use the `local` scope as follows:
```
# This script fails in BuildMaster 2022 and 2023
{
  set local $myvar2  = myval;
}
Log-Information "myvar2" is $myval;
```
Note that `set local` will _always_ create a variable in the local scope, even if one already exists in a higher enclosing scope.

What made BuildMaster 2022's default `set` behavior a bit confusing is that:
* Variables already initialized in a higher scope would be set
* Uninitialized variables would be set in the current scope

We've made default `set` more intuitive in BuildMaster 2023. The new behavior is:
* Variables initialized in a higher scope will still be set
* Uninitialized variables will be created in the global scope

By defaulting `set` to create new variables in the global scope, this will behave as more users expect.

:::(Warning) (OtterScript Changes Risk Mitigation)
It's very unlikely your scripts will be impacted by any of these changes. If you're relying on the BuildMaster 2022 `set` behavior, you may need to modify your scripts to use `set local` prior to upgrading to BuildMaster 2023.
:::

## Other  Features & Notable Changes
* **OtterScript Visual Editor Improvements**; we improved the look/styling of the visual editor and added tag-based searching for statements
* **"Templates" Secure Resource deleted**; if you had a secure resource named "Templates" that was configured to point to our [BuildMasterTemplates](https://proget.inedo.com/feeds/BuildMasterTemplates) feed, it will be removed
* **Application Template Version Change**; if you're using the deprecated [application templates feature](/docs/buildmaster/installation-maintenance/buildmaster-legacy/buildmaster-legacy-application-templates), the template version number has been updated to 2.0.0, which means you'll need to recreate them
* **Application Slug Support**; you can now specify a short, url-friendly name for your application (a "slug"), which will be used in URLS that link to your application

:::(Internal) (No Need to Mention)
## Custom Extensions Changes
BuildMaster 2023 targets `Inedo.SDK-2.4`, which means that you'll need to update the `Inedo.SDK` NuGet package reference in your project to Inedo-SDK 2.4. 

Note that this version of the SDK targets .NET6.

See [creating an extension documentation](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating) to learn more about how to do this.

:::
## Upgrade Process
You should generally perform the upgrade using the same method you used to install.

* On Windows, the most common installation method is using the internet-connected [Inedo Hub](/docs/installation/windows/inedo-hub); see  [HOWTO: Upgrade or Downgrade with the Inedo Hub](/docs/installation/windows/howto-upgrade-downgrade) for more details on how to upgrade.
* On Linux, the most common installation method is using our Docker container; see [Upgrading Docker Containers](/docs/installation/linux/installation-upgrading-docker-containers) for more details.

However, there are other installation options available, including [offline installation](/docs/installation/windows/inedo-hub/offline), [cluster installation](/docs/installation/high-availability-load-balancing/high-availability-load-balancing), and even [manual installation](/docs/installation/windows/manual-installation). If you want to change installation methods, the easiest way is to simply uninstall (by following the process in reverse) and install using the new method.

If you're upgrading from BuildMaster 6.2 or earlier, it's possible BuildMaster was installed with the [legacy installer  (github.com)](https://github.com/Inedo/inedo-docs/blob/c82fd2881e2f1d0c36e77bc8b8b48e2a2c7b75a9/Content/installation/windows/installation-legacy-traditional-installer.md), The Inedo Hub should be able to upgrade these installations, but you may need to uninstall and then reinstall using the Inedo Hub. 

If you are upgrading from BuildMaster 6.1, you will need to perform some additional steps after upgrading to BuildMaster 2023:
1. Remove old ( Legacy SDK) extensions as required 
2. If you are using the `SqlServer 1.0.0` extension, it will not load; delete it and download `SqlServer 2.0.0`
3.  Upgrade/install  extensions as needed

:::(Warning) (Backup Before Upgrading)
You should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).
:::

While you can upgrade from BuidMaster 6.1 and later to BuildMaster 2023 (i.e. there is no need to install intermediate versions), you can only rollback to BuildMaster 2022 without restoring your database.

## Rolling Back

:::(Error) (Database Restore may be required)
If you want to rollback to BuildMaster 7.0 or earlier, you will need to restore the instance to the previous state. See the [Backing Up & Restoring](/docs/installation/backing-up-restoring) documentation to learn more.
:::

However, if you need to rollback to BuildMaster 2022, you can do so without restoring the database by simply using the Inedo Hub. While there are database schema changes, they are all backwards-compatible with BuildMaster 2022, which means you can safely rollback your BuildMaster installation if there's a showstopper bug, and then upgrade later.