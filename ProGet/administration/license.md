---
title: License Restrictions
sequence: 700
keywords: proget, license
show-headings-in-nav: true
---

ProGet is licensed *per installation* on an annual or perpetual basis and has additional restrictions based on the edition as highlighted below:

## Features by Edition {#features-by-edition data-title="Features by Edition"}

{#feature-matrix}
| Features | Free | Basic | Load Balanced - Only | Enterprise (High Availability) |
|-----------------------------------------------------------------------------------------------------------------------------------|----|----|----|----|
| Unlimited Users                                                                                                                   | ✔ | ✔ | ✔ | ✔ |
| [NuGet Feed](/support/documentation/proget/feeds/nuget)                                                                           | ✔ | ✔ | ✔ | ✔ |
| [NuGet Package Symbol Server](/support/documentation/proget/feeds/nuget/symbol-and-source-server)                                 | ✔ | ✔ | ✔ | ✔ |
| [PowerShell Feed](/support/documentation/proget/feeds/powershell)                                                                 | ✔ | ✔ | ✔ | ✔ |
| [Docker Feed](/support/documentation/proget/feeds/docker)                                                                         | ✔ | ✔ | ✔ | ✔ |
| [Ruby Gems Feed](/support/documentation/proget/feeds/rubygem)                                                                     | ✔ | ✔ | ✔ | ✔ |
| [VSIX Feed](/support/documentation/proget/feeds/vsix)                                                                             | ✔ | ✔ | ✔ | ✔ |
| [Chocolatey Feed](/support/documentation/proget/feeds/chocolatey)                                                                 | ✔ | ✔ | ✔ | ✔ |
| [npm Feed](/support/documentation/proget/feeds/npm)                                                                               | ✔ | ✔ | ✔ | ✔ |
| [Bower Feed](/support/documentation/proget/feeds/bower)                                                                           | ✔ | ✔ | ✔ | ✔ |
| [Maven Feed](/support/documentation/proget/feeds/maven)                                                                           | ✔ | ✔ | ✔ | ✔ |
| [PyPI Feed](/support/documentation/proget/feeds/pypi)                                                                             | ✔ | ✔ | ✔ | ✔ |
| [Debian Feed](/support/documentation/proget/feeds/debian)                                                                         | ✔ | ✔ | ✔ | ✔ |
| [Helm Feed](/support/documentation/proget/feeds/helm)                                                                             | ✔ | ✔ | ✔ | ✔ |
| [Deployment Records](/support/documentation/proget/advanced/package-promotion#deployment-records)                                 | ✔ | ✔ | ✔ | ✔ |
| [Connectors to Third-Party Feeds](#connector-limitations)                                                                         | ✔ | ✔ | ✔ | ✔ |
| [Connectors to Other ProGet Instances](#connector-limitations)                                                                    |    | ✔ | ✔ | ✔ |
| [License Scanning and Blocking](/support/documentation/proget/compliance/license-scanning)                                        |    | ✔ | ✔ | ✔ |
| [Connector Filtering](/support/documentation/proget/core-concepts/feeds/connectors#connector-filters)                             |    | ✔ | ✔ | ✔ |
| [Security and Access Controls](/support/documentation/proget/administration/security)                                             |    | ✔ | ✔ | ✔ |
| [Active Directory Integration](/support/documentation/various/ldap/ldap-active-directory)                                         |    | ✔ | ✔ | ✔ |
| [Retention Rules](/support/documentation/proget/administration/retention-rules)                                                   |    | ✔ | ✔ | ✔ |
| [Package Statistics/Metrics](/support/documentation/proget/administration/package-statistics)                                     |    | ✔ | ✔ | ✔ |
| [Cloud Storage](/support/documentation/proget/storage/cloud-storage)                                                              |    | ✔ | ✔ | ✔ |
| [Package Promotion](/support/documentation/proget/advanced/package-promotion)                                                     |    | ✔ | ✔ | ✔ |
| [Vulnerability Scanning](/support/documentation/proget/administration/vulnerability-source)                                       |    | ✔ | ✔ | ✔ |
| [Webhooks](/support/documentation/proget/advanced/webhooks)                                                                       |    | ✔ | ✔ | ✔ |
| [Load Balancing Support](/support/documentation/proget/installation/load-balancing-and-automatic-failover)                        |    |    | ✔ | ✔ |
| [Multiple Web Nodes](/support/documentation/proget/installation/load-balancing-and-automatic-failover#web-node-configuration)     |    |    | ✔ | ✔ |
| [Multiple Index Nodes](/support/documentation/proget/installation/load-balancing-and-automatic-failover#indexing-nodes)           |    |    |    | ✔ |
| [Automatic Failover](/support/documentation/proget/installation/load-balancing-and-automatic-failover)                            |    |    |    | ✔ |
| [Multi-Site Replication](/support/documentation/proget/administration/feed-replication)                                           |    |    |    | ✔ |


## License Keys

For more information on License Key Management and Activation, visit the shared documentation [here](/support/documentation/various/licensing/management).

## ProGet Free Edition Limitations {#free-edition-limitations data-title="Free Edition Limitations"}

*The following limitations are additional to the ones listed in the table above.*

### Client-side Feed Aggegation

Client-side feed aggregation in this context is defined as adding multiple package sources *of the same feed type* configured in a client tool, IDE, or by a build server. While ProGet users are unlimited by default license terms, developers **must not** aggregate feeds from more than one ProGet instance if *any* of them are free edition and used in a production environment. Developers **may** aggreggate feeds from more than one instance even if one of the instances is a free edition for **local** testing purposes, such as: testing/creating packages, developing against the Inedo SDK, or experimenting with any of the [ProGet APIs](/support/documentation/proget/reference/api). However, using a free edition instance in a testing or staging environment is not permitted and each environment would require its own separate license.

### Connector Limitations

ProGet is licensed *per installation*, and a license of ProGet Free Edition grants users one server per installation. While production and commercial use of the free edition is permitted, a free edition **must not** have a connector to a feed hosted by *any other instance* of ProGet and **must not** be the source of a connector from *any other instance* of ProGet (including paid editions).

This limitation does not apply to:

 - public repositories/registries not hosted by ProGet (such as nuget.org, registry.npmjs.org, repo.maven.apache.org, etc.)
 - exempted ProGet instances as per the [section below](#connector-exemption)
 - other feeds in the *same instance* of the free edition
 - temporarily testing or debugging the validity of a connector locally

#### Connector Limitation Exemption {#connector-exemption}

Paid edition instances of ProGet may be granted a special license key that allows its feeds to be used in a connector defined in a free edition instance. For example, [Inedo's Public ProGet instance](https://proget.inedo.com) is allowed to be connected to from the free edition.

If you are hosting a public version of ProGet Basic/Enterprise and would like to allow consumers of your exposed feeds to connect using free edition connectors, see [Requesting an Exemption](#request-exemption) below.

### License Violations{#license-violations data-title="License Violations"}

If a connector limitation violation is detected, a warning banner will be displayed in the UI of the target ProGet instance listing the instances in violation of the license. To remedy this, any connectors to free editions in the target ProGet instance (free or paid) must be removed, and/or the administrator of an offending free instance must be notified in order to remove the connector to the target instance. Once all connectors to the free edition are removed from the target instance and/or all connectors to the target instance are removed from the offending instance(s), the [violations may be cleared](#clearing-violations) to remove the warning banner.

If you believe you are seeing the warning banner in error, see [Requesting an Exemption](#request-exemption) below.

#### Clearing Violations {#clearing-violations}

To clear recorded violations and remove the warning, visit the `Admin` > `License Key & Activation` page within ProGet, edit and re-save the license key. Note, if the offending instance still has a connector, the warning will eventually reappear.

If you are hosting a public version of ProGet Basic/Enterprise and would like to allow consumers of your exposed feeds to connect using free edition connectors, see [Requesting an Exemption](#request-exemption) below.

#### Requesting a ProGet Free Connector Exemption {#request-exemption}

To request a new license key that supports connections from free edition instances, please [submit a support ticket](/support/submit-ticket) indicating that you are requesting a ProGet Free Connector exemption, and include your current license key in the details section.

#### Examples

The following examples help to clarify the rules for ProGet licensing:

- <span class="info-block success">Compliant</span> - 2 distinct business units at Acme Co. both have ProGet Free, both have developers, but do not connect to or reference packages from the other instance
- <span class="info-block error">Noncompliant</span> - 2 distinct business units at Acme Co. both have ProGet Free, both have developers, but a single CI tool downloads packages from each instance
- <span class="info-block success">Compliant</span> - Alana is attempting to code a [Package Filter](https://github.com/Inedo/SampleProGetExtension/blob/master/SampleProGetExtension/PackageFilters/SamplePackageFilter.cs) in custom extension for Acme Co. and wants to test the functionality on her laptop before configuring it on Acme Co.'s ProGet installation
- <span class="info-block error">Noncompliant</span> - Chad has configured Visual Studio with the following sources: nuget.org, Acme Co.'s ProGet Free NuGet Feed1, and Acme Co.'s ProGet Enterprise NuGet Feed2
- <span class="info-block success">Compliant</span> - Alana has configured Visual Studio with the following sources: nuget.org, Acme Co.'s ProGet Free NuGet Feed1, and Acme Co.'s ProGet Free npm Feed2
- <span class="info-block error">Noncompliant</span> - Chad has ProGet Enterprise (up to 5 servers) and "splits" them by configuring 2 servers in a testing environment and 3 in production
- <span class="info-block success">Compliant</span> - Alana has purchased ProGet Enterprise for production and ProGet Basic to test a single-server upgrade in her testing environment