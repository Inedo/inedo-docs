---
title: License Scanning and Blocking
subtitle: License Scanning and Blocking
sequence: 200
keywords: proget, License, Filter
---
By using third-party, open-source packages in your application, you agree to whatever licensing terms the packages' authors specify. In many cases, these licensing terms are benign and permissive. But in some cases, they are restrictive and can open your organization to unexpected legal liabilities.

For example, if you were to use a GNU3-licensed package in your application, you would be required to open source your application, and then license it under GNU3. If your organization failed to do that, it could face a lawsuit from the package authors.

ProGet offers two workflows for managing licensing agreements of third-party, open-source packages.

{.docs}

- **License filtering feature** -the built-in feature allows you to permit or restrict packages from being used based on their license
- **WhiteSource integration**  -the built-in feature allows you to permit or restrict packages from being used based on their license

:::attention {.best-practice}
This feature is available in paid and trial ProGet editions.
:::

##   License Filtering Feature

ProGet ships with an editable list of open-source license types (MIT, GPL3, etc) called known licenses. You then define global and feed-specific rules to block or allow downloads based on a package's license, as well as global or feed-specific rules about whether to block downloads for unknown licenses.

Packages are allowed or blocked based on the following logic:

![](/resources/documentation/proget/fliter-logic.png)

### Known License Types

ProGet's list of known licenses comes from [SPDX](https://spdx.org/licenses), and is periodically updated. You can edit this list by going to Compliance > Licensing > Manage Known Licenses.

A license type consists of the following elements:

{.docs}
- **SPDX Identifier;** a code such as MIT or GPL3 that some package formats use to identify the license used
- **Title;** a description of the license, such as MIT License or General Public License 3.0
- **URLs;** a list of URLs that are associated with this license agreements

![](/resources/documentation/proget/license-rule.png)

### Licensing Rules

A licensing rule consists of a known license type, and an allow or block flag.

![](/resources/documentation/proget/license-list.png)

They can be defined at both the global and feed-level:

{.docs}
- **Global license rules:** Compliance > Licenses
- **Feed-specific license rules:** Feed > Manage Feed > License Filters

If a feed-level rule is defined for a license type, the global rule will be ignored.

{.docs}
- **Global unknown rule:** Advanced Settings > Feeds.AllowUnknownLicenseDownloads
- **Feed-specific unknown rule:** Manage Feed> Unknown Licenses
