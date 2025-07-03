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

![Manage Feed](/resources/docs/proget-feeds-managefeed.png){height="" width="50%"}

Then click "change" in the Feed Features section under "Other Settings".

![Feed Features](/resources/docs/proget-feeds-managefeed-changefeatures.png){height="" width="50%"}

Enable "Display vulnerability information and enforce vulnerability compliance rules" and click "Save".

![Configure Features](/resources/docs/proget-feeds-managefeed-configurefeatures.png){height="" width="50%"}

:::(warning) (⚠ Container Feeds)
For container feeds such as Docker, you also need to make sure "Layer Scanning" is enabled. This is found under the container settings (e.g. "Docker Settings) when navigating to "Manage Feed" following the instructions above. As with the other settings in this step, this is enabled by default.
:::

## Step 2: Find Vulnerable Package

Now, you can navigate to your package of interest and click on the "Vulnerabilities" tab.

![Vulnerabilities](/resources/docs/proget-vulnerabilities-newtonsoft.png){height="" width="50%"}

From here you can click on the vulnerabilities found in your package to get more details and a description of the vulnerability, as well as a link to it's page on [Inedo Security Labs](https://security.inedo.com).

![Vulnerability Details](/resources/docs/proget-vulnerabilities-details.png){height="" width="50%"}

When a package, such as [Newtonsoft.Json 12.0.3](https://www.nuget.org/packages/newtonsoft.json/12.0.3), has a vulnerability, you’ll see this message on the overview page:

![Vulnerability Detected](/resources/docs/proget-vulnerabilities-detected.png){height="" width="50%"}

## Step 3: Assess Vulnerability

Vulnerabilities can be viewed by navigating to a package in your feed and selecting "Vulnerabilities", such as this vulnerability on the [Newtonsoft.Json 12.0.3](https://www.nuget.org/packages/newtonsoft.json/12.0.3) package: *Improper Handling of Exceptional Conditions in Newtonsoft.Json*.

![Assessment Button](/resources/docs/proget-vulnerabilities-assessbutton.png){height="" width="50%"}

To assess a vulnerability, either navigate to the package's vulnerability tab or to "Reporting & SCA" > "Vulnerabilities", find the vulnerability you would like to assess, and click the assessment.

ProGet comes with three built-in assessment types:

* **Ignore** indicates that the vulnerability report is not applicable or irrelevant and therefore allows packages to be downloaded
* **Caution** tells developers to be careful to avoid the vulnerability; packages can be downloaded, but a warning is issued on the web UI
* **Blocked** means that a vulnerability is too severe to allow use and packages cannot be downloaded

![Assess Vulnerability](/resources/docs/proget-vulnerabilities-configureassessment.png){height="" width="50%"}

Select the assessment type, enter a comment, and click the "Save" button.

You can also set an expiry date by navigating to the advanced tab and entering a date in the "Expires" field using the format "mm/dd/yyyy".

![Vulnerability Advanced](/resources/docs/proget-vulnerabilities-advanced.png){height="" width="50%"}

When choosing "Blocked", attempts to download the package from the API will now result in a "404" error, and a successfully blocked package will be shown on your feed "Overview" as shown below:

![Package Blocked](/resources/docs/proget-vulnerabilities-blocked.png){height="" width="50%"}

## Step 4: (Optional) Edit Package Policy Rules

Package compliance policies, available with paid ProGet, is configured on a feed-by-feed basis.

However, you can also set global compliance rules allowing you to block any package with unassessed vulnerabilities.

Navigate to "Administration Overview" and select "Package Policies & Rules" under "Global Components".

Then select "edit" on the right of "Global" policies.

![Edit Policies](/resources/docs/proget-policies-edit.png){height="" width="50%"}

Under the "Vulnerabilities Rules" category, select "edit". Now select the "General" tab and under "Unassessed vulnerability" select "Noncompliant". Then select "Save".

![Unassessed Rule](/resources/docs/proget-policies-noncompliant.png){height="" width="50%"}

Now the compliance rules have been configured, any packages that are assessed to have unassessed vulnerabilities will be categorized as "Blocked" and will not be downloaded, with any attempt to download the package from the API resulting in a "404" error.

![Package Blocked](/resources/docs/proget-vulnerabilities-blocked.png){height="" width="50%"}

## Step 5: (Optional) Add Custom Assessment Types

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

## Step 6: (Optional) Scan Container Vulnerabilities
To scan containers for vulnerabilities, ProGet [extracts and inspects the files within each container image layer](/docs/proget/docker/private-registries) and looks for vulnerable packages that are installed. The "Packages" and "Vulnerabilities" tab of a container image will show these:

![Container Vulnerabilities](/resources/docs/proget-vulnerabilities-container.png){width="50%"}

## ProGet 2023 and Earlier
Vulnerabilities had an overhaul in ProGet 2024. This was available as a preview feature in ProGet 2023.29+, which could be enabled by navigating to "Reporting & SCA" > "Vulnerabilities" and selecting "Enable Vulnerabilities Feature Preview..."

To see how to scan and block packages in earlier versions of ProGet 2023, see  [HOWTO: Scan and Block Packages in ProGet 2023 (archive.org)](https://web.archive.org/web/20231210004603/https://docs.inedo.com/docs/proget-sca-vulnerabilities-howto).

