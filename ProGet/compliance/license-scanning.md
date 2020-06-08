---
title: License Detection and Blocking
subtitle: License Detection and Blocking
sequence: 200
keywords: proget, License, Filter
---
By using third-party, open-source packages in your application, you agree to whatever licensing terms the packages' authors specify. In many cases, these licensing terms are benign and permissive. But in some cases, they are restrictive and can open your organization to unexpected legal liabilities.

For example, if you were to use a GNU3-licensed package in your application, you would be required to open-source your application and then license it under GNU3. If your organization failed to do that, it could face a lawsuit from the package authors.

To protect from the fallout from packages with unwanted licenses, ProGet offers two workflows for managing licensing agreements of third-party, open-source packages:

{.docs}

- **License filtering feature** - This built-in feature lets you to allow or block packages from being used based on their license.
- **[WhiteSource extension](/docs/proget/compliance/whitesource)** - This integration provides a Package Access Rule that will block packages with improper licenses or that don't meet other conditions as defined in your WhiteSource account.

:::attention {.best-practice}
This feature is available in paid and trial ProGet editions.
:::

## License Detection

ProGet can automatically detect the license agreement that a package is using and will display it in a clearly visible manner on the Package Overview page. 

ProGet ships with a comprehensive list of open-source license types (e.g., MIT, GPL3, etc) called known licenses. This list of known licenses comes from [SPDX](https://spdx.org/licenses) and is periodically updated. ProGet's list is *not* exhaustive, so you can edit this list by going to Licenses > License Types.

A license type consists of the following elements:

- **SPDX Identifier:** a code such as MIT or GPL3 that some package formats use to identify the license used
- **Title:** a description of the license, such as MIT License or General Public License 3.0
- **URLs:** a list of URLs that are associated with this license agreements, such as www.opensource.org/licenses/MIT or www.gnu.org/licenses/gpl-3.0-standalone.html

### Unknown Licenses

If a package specifies a SPDX Identifier or URL that's not in your license type list, or if the package doesn't specify a license at all, it's considered to have an unknown license. 

When a package has an unknown license, ProGet will display a warning and give you an opportunity to create a new license type. All other packages with the same SPDX Identifier or URL will then be detected as that license type.

## License Filtering in ProGet

ProGet allows you to define rules to block or allow downloads based on a package's license type. You can also define rules about whether to block downloads for unknown licenses. These rules can be set at the global and feed-specific levels.

Once a license rule is configured, packages with a blocked license will display a red warning on the Package Overview page, and ProGet will not allow the package to be downloaded.

### Licensing Rules

A licensing rule consists of a known license type and an allow or block flag.

![](/resources/documentation/proget/license-rule.png)

They can be defined at both the global and feed-level:

- **Global license rules:** Licenses (on the top navigation)
- **Feed-specific license rules:** Feed > Manage Feed > Scanning & Blocking

If both a global and feed-level rule are defined for a license type, the feed-level rule will be used.

### Blocking Unknown Licenses

ProGet can also be configured to block packages with an unknown license, either at the global or feed-level:

- **Global unknown rule:** Advanced Settings > Feeds.AllowUnknownLicenseDownloads
- **Feed-specific unknown rule:** Feed > Manage Feed > Scanning & Blocking

When ProGet is configured to block packages with unknown licenses, it will also block packages with license types that are not explicitly allowed. For example, if there is only one license rule defined (e.g., allow MIT license types), and you configure ProGet to block unknown licenses then *only* packages with that license type (i.e., MIT) will be downloadable.
