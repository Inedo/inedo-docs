---
title: "Packages & Dependencies"
order: 5
---

:::(Internal) (TODO)
* link to ProGet::SCan operation once uploafed
* link to ProGet integration docs 
* link HOWTO block prerelease
:::
BuildMaster integrates with [ProGet's Software Composition Analysis (SCA)](/docs/proget/sca/builds) to help you track the open-source and third-party components (packages) that your organization uses; and prevent deployment when there are issues like vulnerabilities, license violations, and missing packages. You can also prevent applications that use unstable (pre-release) package dependencies from being deployed past testing environments.

This feature requires a ProGet connection, which you can add under Administration > ProGet Integration.

## Packages & Dependencies Tab
One of navigation tabs available on a build may be Packages & Dependencies: 

![buildmaster-packages-build-page](/resources/docs/buildmaster-packages-build-page.png){height="" width="50%"}

This tab will show information from the linked [Projects and Releases](/docs/proget/sca/builds) in ProGet, including:
* Library packages (i.e. NuGet, npm, etc.) that are used in the build  
* Licenses used by those packages (MIT, BSD, etc.)
* Number of known vulnerabilities in those packages
* Whether there are any [open package issues](/docs/proget/sca/builds) that need to be resolved

You can navigate directly to the release in ProGet by clicking on view release.

This tab will only be displayed if the Packages & Dependencies build feature is enabled in the application, which you can configure under Settings > Configure Build Features.

## Adding to Your Build Scripts

The easiest way to add Packages & Dependencies information to your builds is by checking the Scan for Package Dependencies box in your [Build Script Templates](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts):

![buildmaster-build-script-template](/resources/docs/buildmaster-build-script-template.png){height="" width="50%"}

If you're not using a script template, you can just add the `ProGet::Scan` operation to your build script just after compiling/building your code. The only required input is the path to the project file.

```
ProGet::Scan
(
   Project: src/MyApplication/MyApplication.csproj
);
 ```
 
 ### ProGet::Scan Operation
This operation will:
1. scan your just-built code outputs
2. identify the open-source components that are being used in .NET, npm, and Python projects
3. publish that information to your ProGet server for further analysis
  
Finally, the ProGet project/release information will be associated with the BuildMaster build, so that information can be displayed on the Packages & Dependencies tab and used in Pipeline Stage Requirements.

#### ProGet Project Name & Release Number
We're sorry for the confusing terminology, but we wanted to align ProGet's terminology with [Software Bill of Materials](/docs/proget/sca/builds/proget-sca-sbom-importing-exporting) standards, which use "projects" and "releases".
* ProGet Project = BuildMaster Application
* ProGet Release = BuildMaster Build

By default, `ProGet::Scan` will use `$ApplicationName` for the ProGet Project name,  and `$ReleaseNumber.$BuildNumber` for the Release Number. You can change these settings with the `ProjectName` and `Release` operation parameters.

#### ProGet Connection
`ProGet::Scan` will use the default ProGet connection that you've configured under Administration > ProGet Integration. The ProGet connection should us a [ProGet API Key](/docs/proget/reference-api/proget-apikeys) with "Upload SBOM" permissions.

If you have multiple ProGet connections, you can specify a different connection with the `To` parameter. You can also connect to a ProGet server by specifying an `ApiKey` and `ProGetUrl`.

###  ProGet::Scan vs pgscan
If you've integrated ProGet with a different CI tool before, you may be familiar with `pgscan`; it's a stand-alone, [free and open-source tool](https://github.com/inedo/pgscan) that's used to identify and publish dependency information to ProGet. The `ProGet::Scan` operation is build with the [same code](https://www.nuget.org/packages/Inedo.DependencyScan) and is equivalent to using `pgscan identify`.

We don't recommend using `pgscan` in BuildMaster; it requires the input of several parameters (including secrets like `api-key`) that are best kept as [secured credentials](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials). In addition, it will not associate the BuildMaster build with the ProGet release, which means you won't be able to see details on the Packages & Dependencies tab or use Pipeline Stage Requirements.

To learn more about how `pgscan` works in detail, see ProGet's [SCA and Continuous Integration (CI)](/docs/proget/sca/builds/proget-sca-ci) documentation.


## Pipeline Stage Requirements
After [adding dependency scanning to your build script](#adding-to-your-build-scripts), you'll be able configure a [Pipeline Stage Requirement](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines#stage-requirements-checks-approval-gates) that can prevent deployment when:
*  there are issues like vulnerabilities, license violations, and missing packages
*  unstable (pre-release) package dependencies were used to build the code

To configure this in a pipeline, first edit the pipeline under Settings > Pipelines. Then navigate to the desired stage, click add under Stage Requirements, then select Edit Packages / Dependencies.

![buildmaster-packages-autocheck](/resources/docs/buildmaster-packages-autocheck.png){height="" width="50%"}

These checkboxes will control the requirement for entering the stage.

### ⛔ No Open Issues (OSS License Issues,  Vulnerabilities, etc.)

This pipeline stage requirement will effectively prevent the use of open-source packages that have undesired licenses (like GPL-3) or severe vulnerabilities.

To use this option, you'll first want to configure at least one of two features in ProGet: 
* [Licensing Detection and Blocking in ProGet](/docs/proget/sca/licenses)
* [Vulnerability Scanning and Blocking in ProGet](/docs/proget/sca/vulnerabilities)

After configuring these features, the packages detected by `ProGet::Scan` will then be checked against the license and vulnerability rules in ProGet. If a package isn't in compliance with those rules, a [package issue](/docs/proget/sca/builds) will be  added to the release in ProGet.

#### Best Practice: Block Open Issues Early and Before Production
A build that uses open-source packages with undesired licenses or severe vulnerabilities should not be deployed to production. There's also little value in testing a build with these kind of packages.

Those packages' licenses or vulnerabilities won't change—which means you'll need to:
* adjust the rules that deem them unacceptable in ProGet, which will automatically resolve the issue
* make a one-time exception and resolve the open issue in ProGet
* find a different package to use, creating a new build entirely 

Making these decisions early in the pipeline will save time on testing undeployable builds.

You should also consider adding this stage requirement to the production stage, especially if you have a long release cycle (e.g. weeks). This will ensure that issues that cropped up during the testing cycle won't be missed before production.

#### Best Practice: Don't Force Deploy, Resolve Instead
You may decide that the unwanted packages shouldn't block a deployment; for example, even though there's a severe vulnerability, it won't impact the application because of how it's being used in the code. 

In this case, instead of forcing the promotion in BuildMaster, you should resolve the open issue in ProGet:

![proget-release-issue-resolve](/resources/docs/proget-release-issue-resolve.png)

This will allow you to add comments and provide contextual information about the decision. Once the issues have been resolved in ProGet, you'll be able to deploy in BuildMaster.

### ⛔No Unstable Dependencies (Prevent Prerelease Packages)

BuildMaster will display a warning on builds that use unstable packages, whether those packages are open-source or were created in-house (proprietary).

![buildmaster-packages-unstable](/resources/docs/buildmaster-packages-unstable.png){height="" width=""}

By definition, unstable packages aren't production ready—and builds that use them shouldn't be deployed to production. Adding the "no unstable packages" pipeline stage requirement will effectively prevent these builds from being deployed to that stage.

::: (Info) (What is an Unstable/Pre-release Package?)
To indicate that a package isn't quite ready for production use, developers will add a "pre-release label" like `-beta.1` or `-ci.40` to the package's version number. For example, `5.2.0-rc.2` is a pre-release of `5.2.0`.
:::

There is no additional ProGet configuration required to block unstable packages; BuildMaster can detect which version numbers are unstable and will prevent deployment if any are found.

#### Best Practice: Add Prior to Production
Packages and applications are often developed in tandem, which means that you'll have a lot of builds that use unstable packages. The unstable packages warning will be easy to miss, and as an added precaution, you should consider adding this as a production stage requirerment.

#### Best Practice: Force Only When  Repackaged
Generally speaking, versions `5.2.0-rc.2` and `5.2.0` of a package are different packages altogether. Unless you inspect the contents carefully, there's no easy way to tell what changed between them, and if you're using version `5.2.0-rc.2` of a package—it's safer to create a new build using the stable (`5.2.0`) version.

However, when using [ProGet's Repackaging Feature](/docs/proget/packages/repackaging), you can be assured that the contents of a pre-release and release package are identical. For example, by looking at the history on `Inedo.Upack v2.10`, it's clear that the package was created by repackaging `Inedo.Upack 2.10-rc.1`.

![buildmaster-packages-repackaged](/resources/docs/buildmaster-packages-repackaged.png){height="" width="50%"}

In cases like this—where the unstable packages have been repackaged into stable packages—there's no harm in forcing the build to the desired stage. Doing so may also save everyone time, as you won't need to create and deploy a new build through all environments.

## Closing Releases in ProGet Automatically
When incorporating `ProGet::Scan` into your build scripts, each new build in BuildMaster will create a new release in ProGet. If you create a lot of builds—such as during a CI process—this will add a lot of clutter and make navigating releases in ProGet difficult.

To alleviate this, you can add the Archive or Delete ProGet Release event listener to your pipeline.  

![buildmaster-packages-deleterelease](/resources/docs/buildmaster-packages-deleterelease.png){height="" width=""}

In most cases, you should add this listener to the Build Rejected from Pipeline event, and select Delete Release as the action. There is no value in keeping information about rejected builds that you will never deploy again.