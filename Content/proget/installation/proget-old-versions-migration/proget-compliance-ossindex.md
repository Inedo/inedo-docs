---
title: "Deprecated: OSS Index and ProGet"
order: 1
---

:::(error) (OSS Index Has Been Discontinued)
This article is for users who have configured an OSS Index Vulnerability Source and wish to learn how to migrate. If you haven't configured vulnerability scanning in ProGet yet, check out [HOWTO: Scan and Block Packages in ProGet](/docs/proget/sca/vulnerabilities/howto) to learn more.
:::

After a steady decline in service and data quality, ProGet no longer relies on the free, third-party OSS Index. Instead, ProGet uses industry-leading vulnerability and malicious package detection from our own [Inedo Security Labs](https://security.inedo.com).

This article is for users who have configured an OSS Index Vulnerability Source and wish to switch to ProGet's new and improved vulnerability detection. If you're using ProGet 2022 and earlier, you're welcome to follow the [archived guide for OSS Index integration](https://web.archive.org/web/20230927161336/https://docs.inedo.com/docs/proget-compliance-ossindex), but we recommend upgrading ProGet and using new features instead

## OSS Index vs ProGet's Vulnerability Database

Both OSS Index and Proget's Vulnerability Database help developers identify vulnerabilities in packages, however using Proget's Vulnerability Database offers a number of benefits over the continued use of OSS Index.

OSS Index is a free online service that requires the use of an API or an overnight download, which involves sending all package names/versions to OSS Index. This often results in rate limiting and errors due to server congestion. 

In contrast, Proget's built-in Vulnerability Database's data is available instantly, even to remote packages. If ProGet can connect to the Internet, it downloads updates on a nightly basis. Otherwise, the data is updated every time you update ProGet.
 
## Migrating from OSS Index to ProGet's Vulnerability Database

For users who have configured an OSS Index Vulnerability Source and wish to migrate, ProGet offers two solutions depending on the version you are currently on.

<h3 id="option1">Option 1: Migrating OSS Index Vulnerability Assessments</h3>

:::(info) 
This option requires ProGet 2023.28 or later. For earlier versions, please see [Option 2](#option2)
:::

If OSS Index is configured as a vulnerability source, you will see a large dialog on both the ProGet Home and Vulnerabilities overview page.

To begin the migration process, click "Migrate OSS Index to PGVC"

![OSS Index Warning](/resources/docs/proget-oss-warning.png){height="" width="50%"}

This will open a dialog. This will explain the migration process and confirm the number of vulnerabilities that will be migrated. ProGet will ignore outdated vulnerabilities; vulnerabilities that remain unpatched due to the lack of updates or the continued use of an older version of the software.

![Migrate OSS Index](/resources/docs/proget-oss-migrate.png){height="" width="50%"}

:::(info) (💡 Optional: Dry Run)
You may also initially opt to perform a dry run. This will log the migration, but not perform any changes, allowing you to view the migration before committing to it. 

![Migration Dryrun](/resources/docs/proget-oss-dryrun.png){height="" width="50%"}
:::

Next, confirm the details of the migration and select "Migrate". The migration will perform the following actions:
* Ensure that a ProGet Vulnerability Central (PGVC) vulnerability source exists
* Update PGVC vulnerabilities
* Match and migrate your OSS Index vulnerability assessments to that PGVC source
* Auto-assess remaining PGVC vulnerabilities
* Disassociate OSS Index from your feeds and delete the OSS Index vulnerability source

After migration any blocking rules and vulnerability assessments that were made while using OSS Index will be automatically applied.

![After Migration](/resources/docs/proget-oss-after.png){height="" width="50%"}

<h3 id="option2">Option 2: Delete OSS Index & Enable PGVC</h3>

:::(info) 
This option is recommended for ProGet 2023.27 or older. For earlier versions, please see [Option 1](#option1).
:::

Navigate to "Administration Overview" > "Vulnerabilities Sources" under "Vulnerability Management".

![Administration Overview](/resources/docs/proget-administration-vulnerabilitysources.png){height="" width="50%"}

From here, delete your OSS Index entry.
:::(warning) (⚠ Deleting "OSS Index" will also delete assessments and comments)
However, if your assessment types are configured for automatic assessment, then these would be applied after PGVC is enabled and your packages are scanned.
:::

Next, select "Enable PGVC".
![OSS Index Delete](/resources/docs/proget-oss-delete.png){height="" width="50%"}

By navigating to "Administration Overview" > "Assessment types" under "Vulnerability Management" and selecting an Assessment Type, you can configure auto-assessment criteria based on a vulnerability's severity.

![Configure Assessment](/resources/docs/proget-vulnerability-configure-dialog.png){height="" width="50%"}

Now, navigate to "Reporting & SCA" > "Vulnerabilties" and select "Configure Vulnerability Download Blocking". From here you can add your feeds to be assessed, and then click "Save".

![Configure Vulnerability](/resources/docs/proget-vunerabilitieoverview-configurevulnerability.png){height="" width="50%"}

Finally, navigate to "Administration Overview" > "Scheduled Jobs" under "Additional Logs & Events" and run the "VulnerabilityDownloader", which will add back all the vulnerabilities from PGVC and run auto-assessments.

![Run Vulnerability Downloader](/resources/docs/proget-scheduledjob-vdownloader.png){height="" width="50%"}