---
title: License Detection and Blocking
subtitle: License Detection and Blocking
sequence: 200
keywords: proget, License, Filter
---
By using third-party, open-source packages in your application, you agree to whatever licensing terms the packages' authors specify. In many cases, these licensing terms are benign and permissive. But in some cases, they are restrictive and can open your organization to unexpected legal liabilities.

For example, if you were to use a GNU3-licensed package in your application, you would be required to open-source your application and then license it under GNU3. If your organization failed to do that, it could face a lawsuit from the package authors.

To protect your business from the fallout from packages with unwanted licenses, ProGet offers two workflows for managing licensing agreements of third-party, open-source packages:

{.docs}

- **License filtering feature** - This built-in feature lets you to allow or block packages from being used based on their license.
- **[WhiteSource extension](/docs/proget/compliance/whitesource)** - This integration provides a Package Access Rule that will block packages with improper licenses or that don't meet other conditions as defined in your WhiteSource account.

:::attention {.best-practice}
This feature is available in paid and trial ProGet editions.
:::

## License Detection

ProGet ships with an editable list of open-source license types (e.g., MIT, GPL3, etc) called known licenses. This list of known licenses comes from [SPDX](https://spdx.org/licenses) and is periodically updated. ProGet's list is *not* exhaustive, so you can edit this list by going to Compliance > Licensing > Manage Known Licenses.

A license type consists of the following elements:

{.docs}
- **SPDX Identifier:** a code such as MIT or GPL3 that some package formats use to identify the license used
- **Title:** a description of the license, such as MIT License or General Public License 3.0
- **URLs:** a list of URLs that are associated with this license agreements

By default, ProGet will display an orange warning on the Package Overview page to alert you that your packages has a known or unknown license associated with it. If the license type is unknown, you can one in ProGet. No packages are blocked by default. To create a feed-level or global block or allow rule, you must create a license filter.

##   License Filtering in ProGet

ProGet allows you to define rules to block or allow downloads based on a package's license type. You can also define rules about whether to block downloads for unknown licenses. These rules can be set at the global and feed-specific levels.

Once a license rule is configured, packages with a blocked license will display a red warning on the Package Overview page, and ProGet will not allow the package to be downloaded.

### Licensing Rules

A licensing rule consists of a known license type and an allow or block flag.

![](/resources/documentation/proget/license-rule.png)

They can be defined at both the global and feed-level:

{.docs}
- **Global license rules:** Compliance > Licenses
- **Feed-specific license rules:** Feed > Manage Feed > License Filters

If a feed-level rule is defined for a license type, the global rule will be ignored.

{.docs}
- **Global unknown rule:** Advanced Settings > Feeds.AllowUnknownLicenseDownloads
- **Feed-specific unknown rule:** Manage Feed> Unknown Licenses

If you have assigned a license to an unknown URL but the Package Overview page is still displaying an unknown license warning, set a rule allow the assigned license type (e.g., Apache-2.0).
