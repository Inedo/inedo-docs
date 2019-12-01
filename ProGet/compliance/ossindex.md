---
title: Sonatype OSS Index Integration
subtitle: Integrating ProGet with OSS Index
sequence: 400
show-headings-in-nav: true
---

The Sonatype extension provides a Vulnerability Source that will automatically import vulnerability reports from various public databases using [Sonatype's OSS Index](https://ossindex.sonatype.org). This feature is available in paid and trial ProGet editions.

## Installing the Sonatype Extension {#extension data-title="Sonatype Extension"}

Integrating OSS Index with ProGet is easy: Simply navigate to the Admin > Extensions page in your instance of ProGet and click on the Sonatype extension to install it.

If your instance doesn't have internet access, you can [manually install the Sonatype extension](/docs/proget/administration/extensions#manual-install) after downloading the [Sonatype Package](https://proget.inedo.com/feeds/Extensions/inedox/Sonatype).


## Connecting to OSS Index {#connect data-title="Connecting to OSS Index"}
If you're not an OSS Index user, you'll need to make an account and request a trial license or purchase a professional license. This can be done by visiting https://ossindex.sonatype.org/ > selecting the user icon in the top right corner > Register for an account.

:::attention {.best-practice}
**Note**: Sonatype [recently acquired](https://www.sonatype.com/sonatype-acquires-vor-security-announces-lifecycle-xc) Vor Security; in ProGet v5.0 and earlier, OSS Index was Vor Security. If you were previously a Vor Security user, your account has been automatically migrated over to OSS Index.
:::

Once logged into OSS Index you can copy the API token.

After retrieving the API token from OSS Index, set it as your Vulnerability Source in ProGet by going to *Administration* > *Manage Vulnerability Sources* > *Create Vulnerability Source*.

## Create an Assessment {#managing-assessments data-title="Create an Assessment"}

To create a new Assessment type go *Administration* > *Manage Assessment Types* > *Create Assessment Type*.

:::attention {.analogy}
![](/resources/images/icons/analogy.png) When a vulnerability is detected, it is automatically assigned to the unassessed type, and will require user assessment before being usable.
:::

## Feed Level Configuration {#feed-level data-title="Feed Level Configuration"}

Vulnerability sources are scoped at the feed level and can be added on a feed-by-feed basis. Since [Security and Access Controls](/docs/proget/administration/security) are also scoped at the feed level, you are able to permit and restrict access to who is allowed to assess vulnerabilities.

Go to the *feeds* tab and select a feed to add the vulnerability source to. Then select *Manage Feed* > *add source*.

Once the source is added, any package vulnerabilities found will be labeled *Unassessed*, and will be available for assessment.

## Assessing Vulnerabilities {#assess-vuln data-title="Assessing Vulnerabilities"}

After you've added a vulnerability source, any known vulnerabilities will be available for assessment. These are viewed by clicking the *Vulnerabilities* tab.

  Select any of the vulnerabilities listed to view additional details, and assign an assessment status. Click *assess* to select a new assessment type.

:::attention {.best-practice}
Assessments can have a set expiration which will force reassessment. This ensures that compliance needs are continually addressed and that security standards are upheld as development continues.
:::
