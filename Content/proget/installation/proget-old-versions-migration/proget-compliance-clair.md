---
title: "Deprecated: Clair and ProGet"
nav-title: "Clair Integration"
order: 2
---

:::(error) (Clair Integration was Discontinued in ProGet 2024)
This article is for users who have configured ProGet's Clair integration and wish to learn how to switch to ProGet's built-in container scanning feature. If you haven't configured container scanning and vulnerability scanning in ProGet yet, check out [HOWTO: Scan and Block Packages in ProGet](/docs/proget/sca/vulnerabilities/howto) to learn more.
:::

ProGet no longer relies on Clair, the third-party container-scanning tool, to find vulnerabilities in containers. Instead, this functionality is built-in, and uses industry-leading vulnerability scanning and detection from our own [Inedo Security Labs](https://security.inedo.com).

If you're using ProGet 2023 and earlier, you're welcome to follow the [archived guide for Clair integration](https://web.archive.org/web/20230927161336/https://docs.inedo.com/docs/proget-compliance-clair), but we recommend upgrading ProGet and using new features instead.

## Clair vs ProGet's Built-in Container Scanning

Prior to 2023.29, ProGet did not have container scanning capabilities. Instead, we relied on a free and open-source tool called Clair to perform this function. Clair is a standalone, third-party tool that is somewhat complex to configure and requires additional resources to run and manage.

As a standalone tool, one of the main issues is that containers needed to be uploaded to Clair on a nightly basis. This meant vulnerability detection was often delayed, and both Clair and ProGet would be taxed during the nightly scan.

In contrast, ProGet's built-in container scanning happens immediately after upload. Instead of needing to scan a container multiple times for packages, ProGet "remembers" which packages a container has - and can then easily detect vulnerabilities in those packages.
 
 ## Migrating from Clair to ProGet's Vulnerability Database

To migrate from Clair, you simply need to delete the Clair vulnerability source and enable ProGet 2024 Preview Vulnerability features.

First, navigate to "Administration Overview" > "Vulnerabilities Sources" under "Vulnerability Management".

![Administration Overview](/resources/docs/proget-administration-vulnerabilitysources.png){height="" width="50%"}

From here, delete the Clair entry.

:::(warning) (⚠ Deleting "Clair" will also delete assessments and comments)
However, if your assessment types are configured for automatic assessment, then vulnerabilities detected in containers will be automatically assessed after the container is scanned.
:::

Next, navigate to "Reporting & SCA" > "Vulnerabilities" and select "Enable Vulnerabilities Feature Preview..."

![Enable Preview](/resources/docs/proget-enable-preview.png){height="" width="50%"}

Enabling the Preview Feature will also download the latest vulnerability definitions from [Inedo Security Labs](https://security.inedo.com)

Finally, make sure "Layer Scanning" is enabled. Navigate to your Docker feeds, and select "Manage Feed".

![Manage Feed](/resources/docs/proget-docker-managefeed.png){height="" width="50%"}

Then, navigate to "Layer Scanning":

![Layer Scanning](/resources/docs/proget-feeds-layerscanning.png){height="" width="50%"}

If Layer Scanning is disabled, check "Enable layer scanning". ProGet will warn that it may take some time depending on the volume of images in your repository. When you are ready to perform this scan, select "Save".

![Layer Scanning Dialog](/resources/docs/proget-feeds-layerscanning-dialog.png){height="" width="50%"}

To scan containers for vulnerabilities, ProGet [extracts and inspects the files within each container image layer](/docs/proget/docker/private-registries) and looks for vulnerable packages that are installed. After the scan, the "Packages" and "Vulnerabilities" tab of a container image will show these:

![Container Vulnerabilities](/resources/docs/proget-vulnerabilities-container.png){height="" width="50%"}
