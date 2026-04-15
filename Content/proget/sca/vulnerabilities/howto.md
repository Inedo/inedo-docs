---
title: "HOWTO: Scan and Block Packages in ProGet"
order: 1
---

Configuring ProGet to automatically scan third-party open-source packages and container images for vulnerabilities is simple and straightforward.

This guide will explain how to scan and assess vulnerabilities, and how you can configure blocking rules and assessments when a package is found to be vulnerable.

## Step 1: Enable Vulnerabilities Feed Feature 

:::(info) (📄 Note)
This step is enabled by default. Unless this feature is disabled on your instance, you can skip to step 2. 
:::

To configure vulnerability blocking on a feed, first navigate to the feed and click the "Feed Properties" tab. 

![](resources/docs/proget-feeds-managefeed.png){height="" width="50%"}

Then click "change" in the Feed Features section under "Other Settings". 

![](resources/docs/proget-feeds-managefeed-changefeatures.png){height="" width="50%"}

Enable "Display vulnerability information and enforce vulnerability compliance rules" and click "Save". 

![](resources/docs/proget-feeds-managefeed-configurefeatures.png){height="" width="50%"}


:::(Warning) (⚠ Container Feeds)
For container feeds such as Docker, you also need to make sure "Layer Scanning" is enabled. This is found under the container settings (e.g. "Docker Settings) when navigating to "Manage Feed" following the instructions above. As with the other settings in this step, this is enabled by default. 
:::

## Step 2: Use Vulnerable Package 

When packages are used in your Projects and Builds in ProGet, they will be auto assessed. You can view any identified vulnerabilities by navigating to "Reporting & SCA" and then selecting the Build. 

![](resources/docs/proget-builds-overview.png){height="" width="50%"}

From here, select the "Vulnerabilities" tab to view all identified vulnerabilities in the Build.  

![](resources/docs/proget-builds-vulnerabilities.png){height="" width="50%"}

## Step 3: Scan your Build 

You can scan your Build using pgutil and the builds scan command. This will analyze the packages used in the build for any vulnerabilities.  

![](resources/docs/proget-builds-scan.png){height="" width="50%"}

Here we can see the a number of vulnerabilities have appeared in the scan, resulting in the affected package being assessed as non-compliant. This will prevent the build from being deployed to the next stage. 

![](resources/docs/proget-builds-scan-noncompliant.png){height="" width="50%"}

## Step 4: (Optional) Run an audit 

You can also run pgutil builds audit right before deploying to production to identify any vulnerabilities that may have surfaced since you originally ran pgutil builds scan. 

<screenshot of pgutil builds audit on the build> 

## Step 5: (Optional) Block the Package 

To prevent vulnerable packages from entering your development and being used in production to begin with, you can block these from being downloaded.  

Navigate to the vulnerable package and select “Set Package Status” from the menu on the right. 

<image demonstrating above> 

From here select “Always block downloads” and select “Set Status” 

<image demonstrating above> 

This will prevent the package from being downloaded. 

<image demonstrating above> 

## Step 6: (Optional) Edit Package Policy Rules

Package compliance policies, available with paid ProGet, is configured on a feed-by-feed basis.

However, you can also set global compliance rules allowing you to block any package with unassessed vulnerabilities.

Navigate to "Administration Overview" and select "Package Policies & Rules" under "Global Components".

Then select "edit" on the right of "Global" policies.

![Edit Policies](/resources/docs/proget-policies-edit.png){height="" width="50%"}

Under the "Vulnerabilities Rules" category, select "edit". Now select the "General" tab and under "Unassessed vulnerability" select "Noncompliant". Then select "Save".

![Unassessed Rule](/resources/docs/proget-policies-noncompliant.png){height="" width="50%"}

Now the compliance rules have been configured, any packages that are assessed to have unassessed vulnerabilities will be categorized as "Blocked" and will not be downloaded, with any attempt to download the package from the API resulting in a "404" error.

![Package Blocked](/resources/docs/proget-vulnerabilities-blocked.png){height="" width="50%"}

## Step 7: (Optional) Add Custom Assessment Types

We recommend using auto assessment in combination with a [package approval workflow](https://blog.inedo.com/nuget/package-approval-workflow).

You can edit or create your own assessment type and set up auto-assessment by navigating to "Administration Overview" > "Vulnerability Assessment Types" under "Global Components".

![Assessment Settings](/resources/docs/proget-vulnerabilities-assessmentsettings.png){height="" width="50%"}

From here you can create an assessment type by clicking "Create Custom Assessment Type". By default, the assessment types of Caution, Blocked, and Ignore will be presented and automated.

![Assessment Types](/resources/docs/proget-vulnerabilities-manageassessment.png){height="" width="50%"}

Auto assessment can be customized to your preferences. However, if you’re unsure of what the best option is; we recommend reading our page on [Vulnerability Scanning & Blocking](/docs/proget/sca/vulnerabilities#assessing-vulnerabilities).

![Custom Type](/resources/docs/proget-vulnerabilities-customassessment.png){height="" width="50%"}

Once you have entered the details of your Assessment Type, click "Save".

:::(info) (📄 Expiration (days))
Note that expiration days will set the expiry date on "auto-assess" only.
:::

## Step 8: (Optional) Scan Container Vulnerabilities
To scan containers for vulnerabilities, ProGet [extracts and inspects the files within each container image layer](/docs/proget/docker/private-registries) and looks for vulnerable packages that are installed. The "Packages" and "Vulnerabilities" tab of a container image will show these:

![Container Vulnerabilities](/resources/docs/proget-vulnerabilities-container.png){width="50%"}

## ProGet 2023 and Earlier

### ProGet 2024 & 2025
Vulnerabilities had an overhaul in ProGet 2024. This was available as a preview feature in ProGet 2023.29+, which could be enabled by navigating to "Reporting & SCA" > "Vulnerabilities" and selecting "Enable Vulnerabilities Feature Preview...". To see how to scan and block packages in ProGet 2024 and 2025 see [HOWTO: Scan and Block Packages in ProGet 2024 & 2025 (archive.org)](https://web.archive.org/web/20251119061249/https://docs.inedo.com/docs/proget/sca/vulnerabilities).

### ProGet 2023 and Earlier
To see how to scan and block packages in earlier versions of ProGet 2023, see  [HOWTO: Scan and Block Packages in ProGet 2023 (archive.org)](https://web.archive.org/web/20231210004603/https://docs.inedo.com/docs/proget-sca-vulnerabilities-howto).

