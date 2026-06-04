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

![](/resources/docs/proget-feeds-managefeed.png){height="" width="50%"}

Then click "change" in the Feed Features section under "Other Settings". 

![](/resources/docs/proget-feeds-managefeed-changefeatures.png){height="" width="50%"}

Enable "Display vulnerability information and enforce vulnerability compliance rules" and click "Save". 

![](/resources/docs/proget-feeds-managefeed-configurefeatures.png){height="" width="50%"}


:::(Warning) (⚠ Container Feeds)
For container feeds such as Docker, you also need to make sure "Layer Scanning" is enabled. This is found under the container settings (e.g. "Docker Settings) when navigating to "Manage Feed" following the instructions above. As with the other settings in this step, this is enabled by default. 
:::

## Step 2: Use Vulnerable Package 

When packages are used in your Projects and Builds in ProGet, they will be auto assessed. You can view any identified vulnerabilities by navigating to "Reporting & SCA" and then selecting the Build. 

![Build Overview](/resources/docs/proget-builds-overview.png){height="" width="50%"}

From here, select the "Vulnerabilities" tab to view all identified vulnerabilities in the Build.  

![Build Vulnerabilities](/resources/docs/proget-builds-vulnerabilities.png){height="" width="50%"}

## Step 3: Scan your Build 

You can scan your Build using [pgutil](/docs/proget/api/pgutil) and the [`pgutil builds scan`](/docs/proget/api/sca/builds/scan) command. This will analyze the packages used in the build for any vulnerabilities.  

![Builds Scan](/resources/docs/proget-builds-scan.png){height="" width="50%"}

Here we can see the a number of vulnerabilities have appeared in the scan, resulting in the affected package being assessed as Noncompliant. This will prevent the build from being deployed to the next stage. 

![Non Compliant](/resources/docs/proget-builds-scan-noncompliant.png){height="" width="50%"}

## Step 4: (Optional) Run an audit 

You can also run [`pgutil builds audit`](/docs/proget/api/sca/builds/analyze) right before deploying to production to identify any vulnerabilities that may have surfaced since you originally ran `pgutil builds scan`. 

![Audit Non Compliant](/resources/docs/proget-builds-scan-noncompliant.png){height="" width="50%"}

## Step 5: (Optional) Block Noncompliant Packages 

To prevent vulnerable packages from entering your development and being used in production to begin with, you can block noncompliant packages from being downloaded at the feed level.  

Navigate to the feed and select "Policies and Blocking".

![Select Policies](/resources/docs/proget-feed-policies-select.png){height="" width="50%"}

From here scroll to the bottom and select "Change Blocking Settings".

![Blocking Policies](/resources/docs/proget-policies-blocking.png){height="" width="50%"}

Then from "Feed Setting" select "Block noncompliant packagess" and select "Save". 

![Feed Block](/resources/docs/proget-feed-block.png){height="" width="50%"}

This will prevent noncompliant packages from being downloaded. 

## Step 6: (Optional) Edit Package Policy Rules

Package compliance policies, available with paid ProGet, is configured on a feed-by-feed basis.

However, you can also set global compliance rules allowing you to block any package with unassessed vulnerabilities.

Navigate to "Administration Overview" and select "Package Policies & Rules" under "Global Components".

Then select "edit" on the right of "Global" policies.

![Edit Policies](/resources/docs/proget-policies-edit.png){height="" width="50%"}

Under the "Vulnerabilities Rules" category, select "edit". Now select the "General" tab and under "Unassessed vulnerability" select "Noncompliant". Then select "Save".

![Unassessed Rule](/resources/docs/proget-policies-noncompliant.png){height="" width="50%"}

Now the compliance rules have been configured, any packages that are assessed to have unassessed vulnerabilities will be categorized as "Blocked" and will not be downloaded, with any attempt to download the package from the API resulting in a "404" error.

:::(Internal) (Update Screenshot)
![Package Blocked](/resources/docs/proget-vulnerabilities-blocked.png){height="" width="50%"}
:::

## Step 7: (Optional) Add Custom Assessment Types

You can customize assessment types in ProGet to better reflect how your organization evaluates and responds to vulnerabilities. This is especially useful when default assessments don’t align with your workflows, risk tolerance, or the way your teams manage and prioritize remediation.

To edit or create your own assessment types, navigate to "Administration Overview" > "Vulnerabilities & Assessment Types" under "Global Components".

![Assessment Settings](/resources/docs/proget-vulnerabilities-assessmentsettings.png){height="" width="50%"}

From here you can create an assessment type by clicking "Create Custom Assessment Type". By default, the assessment types of "Monitor", "Remediate", and "Contain" will be presented and automated.

![Assessment Types](/resources/docs/proget-vulnerabilities-manageassessment.png){height="" width="50%"}

Auto assessment can be customized to your preferences. However, if you’re unsure of what the best option is; we recommend reading our page on [Vulnerability Scanning & Blocking](/docs/proget/sca/vulnerabilities#assessing-vulnerabilities).

![Custom Type](/resources/docs/proget-vulnerabilities-customassessment.png){height="" width="50%"}

Once you have entered the details of your Assessment Type, click "Save".

## Step 8: (Optional) Scan Container Vulnerabilities
To scan containers for vulnerabilities, ProGet [extracts and inspects the files within each container image layer](/docs/proget/docker/private-registries) and looks for vulnerable packages that are installed. The "Packages" and "Vulnerabilities" tab of a container image will show these:

![Container Vulnerabilities](/resources/docs/proget-vulnerabilities-container.png){width="50%"}

## ProGet 2023 and Earlier

### ProGet 2024 & 2025
Vulnerabilities had an overhaul in ProGet 2024. This was available as a preview feature in ProGet 2023.29+, which could be enabled by navigating to "Reporting & SCA" > "Vulnerabilities" and selecting "Enable Vulnerabilities Feature Preview...". To see how to scan and block packages in ProGet 2024 and 2025 see [HOWTO: Scan and Block Packages in ProGet 2024 & 2025 (archive.org)](https://web.archive.org/web/20251119061249/https://docs.inedo.com/docs/proget/sca/vulnerabilities).

### ProGet 2023 and Earlier
To see how to scan and block packages in earlier versions of ProGet 2023, see  [HOWTO: Scan and Block Packages in ProGet 2023 (archive.org)](https://web.archive.org/web/20231210004603/https://docs.inedo.com/docs/proget-sca-vulnerabilities-howto).

