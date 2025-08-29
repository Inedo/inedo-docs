---
title: "Policies & Compliance Rules"
order: 1
---

Policies & Compliance Rules make it easy to clearly define what "compliant" means for open-source packages in different contexts. You can easily define rules about licensing, vulnerability, deprecation, etc. to block download/usage of noncompliant packages, while also having flexibility to create exceptions for known packages and "warn" for packages that are in-between.  

:::(Info) (🔒 ProGet Enterprise Feature)
Although Policies & Compliance Rules are available in all editions of ProGet, the feature is limited in ProGet Free and ProGet Basic editions. See [License Restrictions](/docs/proget/administration/license) to learn more.
:::

## Feed Policies & Packages

Feeds typically use two sets of rules (global- and feed-level) to determine whether packages are compliant, noncompliant, or neither (i.e. "warn"). Rules created at the feed-level will override global rules, which means you can combine rules to say, for example, "GPL 3 licenses are noncompliant, except in this feed."

A set of rules is called a Policy, and you create additional Policies and use them across different feeds. Similar to Global and Feed-level Policies, the rules in a Shared policy can override other rules, providing a lot of flexibility for determining compliance. See [Shared & Multiple Policies](#) to learn more.

### Viewing and Changing Feed Policies

The easiest way to view or change the Policies that apply to a feed is by navigating to Feeds > Manage Feeds > Policies and Blocking.

![](/resources/docs/proget-policies-sharedpolicy-blocked.png){height="" width="50%"}

This page combines the feed's Policies into a single view, so you can easily see which rules will apply to packages in the feed. You can also see and edit an individual policy under Admin > Policies.

### Package Analysis & Compliance

Based on the feed's policies, packages will display a banner that indicates compliant, noncompliant, or warn.

![](/resources/docs/proget-accord-licence-blocked.png){height="" width="50%"}


This status is the result of a *package analysis*, which occurs:
 * Every evening using the Package Analyzer [Scheduled Job](/docs/proget/administration/service#scheduled-jobs)
 * Immediately after being added to a feed
 * Upon download, if there was a recent change to the feed's policies

You can also manually perform a package analysis by navigating to a package and selecting Reanalyze Package from the top-right drop-down button menu.

![](/resources/docs/proget-policies-renalyze-package.png){height="" width="50%"}

A re-analysis will provide a detailed log that can help troubleshoot rules across multiple policies.

## Compliance Rules

A policy contains a number of rules that are used to analyze whether packages are compliant, noncompliant, or neither (i.e. "warn"). 

When a package is analyzed, it's evaluated against each rule individually. The result of a rule evaluation will be compliant, noncompliant, or warn, and the package's compliance status as a whole will be:
 * ✔ Compliant - all rules were compliant
 * ❌ Noncompliant - at least one rule was noncompliant
 * ⚠ Warn - at least one rule was warn, and no rules were noncompliant

The analysis logic gets a little more complicated with [rule exemptions](#rule-exemptions) and [multiple policies](#), but in all cases, there is a fixed number of pre-defined rules that you can configure on a policy.

### Vulnerability Rules

Vulnerability rules determine compliance based on the vulnerabilities that were detected in a package, and more specifically, how you assessed those vulnerabilities.

::: (Info) (👨‍🏫 Refresher on Vulnerability Assessments)
When ProGet first detects a vulnerability in a package, the vulnerability will be considered "Unassessed", and you can select "Severe", "Warn", "Ignore", or a custom assessment type you've added to ProGet.  ProGet can automatically assess newly-detected vulnerabilities for you based Inedo Security Lab's severity score.

Note that assessments can also expire, which means a vulnerability you originally assessed as "Ignore" could be considered "Unassessed" later.
:::

There are two types of vulnerability rules you can configure:
 * **Unassessed Vulnerability Rule** is evaluated when the package has a vulnerability you haven't assessed, or with an expired assessment; this defaults to *Warn*
 * **Assessed Vulnerability Rules** are evaluated based on how the package's vulnerabilities have been assessed:
   * **Severe** assessment types default to *noncompliant*
   * **Warn** assessment types default to *Warn*
   * **Ignore** assessment types default to *Compliant*
   * **Custom** assessment types default to *Warn*

Defining vulnerability rules may be particularly useful when you use custom assessment types. For example, you may wish to have ProGet's auto-assess feature "temporarily" assess a vulnerability until you can manually review/reassess.

![](/resources/docs/proget-policies-vulnerability-rules.png){height="" width="50%"}

If multiple vulnerabilities were detected on a package, they will each be evaluated against the rules separately. For example, if one vulnerability was assessed as Ignore, but another is Severe, the package would still be considered noncompliant.

For more context on [what package vulnerabilities are and how to handle them](https://blog.inedo.com/nuget/vulnerabilities/), see our article on the Inedo blog.

### License Rules

License rules determine compliance based on the licenses that were detected in a package, or the lack of detected licenses.

::: (Info) (👨‍🏫 Refresher on License Detection)
ProGet includes a comprehensive database of open-source licenses and detection methods to help match packages to licenses. You can also add your license definitions; see [How does ProGet detect Licenses?](/docs/proget/sca/licenses#how-does-proget-detect-licenses) to learn more.
:::

There are three types of license rules you can configure:
* **Unknown License Rule** is evaluated when a license couldn't be detected on the package; this defaults to *Warn*
* **Specified License Rules** consist of a license type (e.g. MIT, GPL) and a status (Compliant, noncompliant, Warn), and are evaluated matching package licenses
* **Unspecified License Rule** is evaluated when when there are no Specified License Rules; this defaults to *Compliant*

Although ProGet does not include any default rules for Specified Licenses, the Global policy created on new installations of ProGet includes rules that warn about commonly unwanted licenses (GPL, etc).

![](/resources/docs/policies-default-license-rules.png){height="" width="50%"}


#### Creating Whitelist, Greylist, and Blacklist Policies
The *Unspecified License Rule* primarily serves to let you define a Whitelist-, Greylist-, and Blacklist-style policies. 

For example:
 * **Whitelist** would set Unspecified to noncompliant, which means a Specified Rule would need to be created for all permitted licenses
 * **Greylist** would set Unspecified to Warn, and Specified Rule should generally be created permitted and unwanted licenses
 * **Blacklist** would set Unspecified to Compliant, and a Specified Rule would be created for all unwanted licenses

#### Packages with Multiple Licenses
If a package has multiple licenses detected, this means that the consumer can choose which license they'd like to use. When this is the case, ProGet will use the *more compliant* license for evaluation purposes. For example, if a package is dual-licensed with GPL and MIT, it would still be considered compliant if GPL licenses were noncompliant and MIT licenses were compliant.

### Other Rules (Listed, Deprecated, Latest Patch)

ProGet includes three additional rules that can help determine compliance.

#### Unlisted Rule

Packages in ProGet and public repositories like NuGet.org can be marked as "unlisted", which means they shouldn't be used in new projects. PyPI.org and rubygems.org call this "yanked".

Unlisted packages are displayed with a small "invisible" icon in the ProGet web interface, and some clients (like `dotnet nuget`, Visual Studio, `pip`, `gem`, etc.) will "decrease the visibility in search results" of unlisted packages.

The Unlisted Rule is applied to packages that are marked as "unlisted", and it defaults to *Warn*

#### Deprecated Rule

Similar to "unlisted", packages in ProGet and public repositories like NuGet.org and npmjs.org can be marked as "deprecated", which means they generally shouldn't be used in any project. 

Deprecated packages are displayed with a small "caution" icon in the ProGet web interface, and some clients (Visual Studio, `dotnet nuget,` `npm`) will also warn about deprecated packages in the project.

The Deprecated Rule is applied to packages that are marked as "deprecated", and it defaults to *Warn*.

#### Latest Patch Version Rule

*Latest Patch Version Rule is a ProGet 2025 preview feature that's available in ProGet 2024.12.*

Packages may have a newer patch version available, which means they generally should be upgraded to fix bugs or performance problems. For example, if you're currently using 1.3.0 or 1.3.1, then 1.3.2 would be considered a new patch version. Some clients (Visual Studio, `npm`, etc.) will also advise to update to newer patch versions.

To determine if a package is compliant with the Latest Patch Version Rule, ProGet will either inspect local packages in the feed or utilize the [OSS Metadata Cache](#oss-metadata-updating-caching) if the package originated from a public repository like nuget.org or npmjs.org.

This rule is only evaluated in ProGet Enterprise and defaults to *Compliant*.


### Other & Custom Rules

These rules are just the starting point, and we'd love to get your feedback on what else we could add. Perhaps we could even allow users to create their own rules using a plug-in?

Just reach out to us via our [support channel](https://inedo.com/support), preferably using the [Inedo Forums](https://forums.inedo.com), so everyone can benefit from the discussion.

Our current ideas include a "Latest Patch Version" rule, which would make sure that a package is the latest version. So if you were to use Newtonsoft.Json 13.0.2, you could be warned that it's not the latest "patch" version (13.0.3 is current, as of this writing).

## Rule Exceptions

Although you configure complex sets of rules using [Shared & Multiple Policies](#), you can also create exceptions to those rules for certain packages. These can be permanent or temporary, and apply to a range of packages and versions.

For example, private packages that you create won't really have a license agreement, which means ProGet may consider them unlicensed and thus noncompliant. Although you could create a "fake" license definition for your own packages, it's clearer to exempt your own packages (i.e. `MyCorp.*` ) from license rule analysis:

![](/resources/docs/proget-exception-name-wildcard.png){height="" width="50%"}

An example of a temporary exemption is a deprecated third-party package. If the author deprecates a package that you have no intention of upgrading for at least a year, an expiring exemption would allow you to effectively "ignore" those compliance warnings.

![](/resources/docs/proget-exception-version-range.png){height="" width="50%"}

When using multiple policies, note that exemptions are not "overridden" like rules. This means that if you define an exception in the Global policy, it cannot be "undone" at a feed-level.

### Name Wildcards and Version Filters

An exception can be for a single package, or for all packages with a matching name and version range.

Names support wildcards. For example, `MyCorp.*` will match all packages that start with `MyCorp.`. You can specify multiple wildcards, although that may be confusing and isn't recommended.

Versions support wildcards in the same format as names, and they also support interval range syntax, which is slightly more complicated. For example:

* `1.3.1` means "exactly version 1.3.1"
* `1.3.*` means "any version that starts with 1.3."
* `[1.3.0,)` means "1.3.0 or greater"
* `(1.4.5,)` means "greater than 1.4.5"
* `(,5.0.0)` means "less than 5.0.0"
* `[1.0,2.0)` means "1.0 or later, up to (but not including) 2.0"

You can combine multiple expressions as a comma-separated list (e.g. `4.2.1, [4.5.1,)`), but that gets pretty confusing and isn't recommended.

## Shared & Multiple Policies (Advanced)

In addition to global and feed-level policies, you can create a Shared Policy that can be used across multiple feeds that require similar rules or exemptions.

These shared policies can be created on Manage Feed > Policies & Blocking, or Administration > Manage Policies.

### Naming Convention

ProGet uses the policy's name to determine its type.

 * **Global Policy** is the policy named "Global", and is always used by all feeds
 * **Feed Policies** have the same name as a feed, and will always be used by that feed
 * **Shared Policies** have any other name, and are used only by the feeds you associate them with

The UI does not *enforce* these naming conventions, which means you can rename the Global policy to "convert" it to a shared policy.

:::(Internal) (Not Yet Implemented)
#### Removing Global Policy

The Global policy is used by all feeds, and a feed cannot "opt out" of this application. However, you could delete or rename the global policy if you don't want this behavior.

[screenshot of renaming global policy w/ warning]

You could then create as many shared policies as needed.

#### Exporting and Importing

You can export all of the policies configured in ProGet as a .zip archive by clicking the "export" button on policies. This file can be used to import policies into a new ProGet instance or as a means to back up/restore policies within a single instance. 

Note that the format of this archive file is not documented, and it's not intended to be edited/created by anything but ProGet.

When importing a policy backup, ProGet will overwrite existing policies or create ones if they don't exist. Importing does not impact vulnerability assessments or any associated policy.
:::

## OSS Metadata Updating & Caching

*OSS Metadata Updating & Caching is a ProGet 2025 preview feature that's available in ProGet 2024.12. Only NuGet and npm packages are currently supported, but we'd be happy to add another feed type if you're interested in trying this feature.*

ProGet can cache and update metadata for open-source packages you're using from public repositories like NuGet.org and npmjs.org. This helps you ensure the packages you're using haven't been deprecated, unlisted, or are outdated. 

This is a ProGet Enterprise feature, but you can still configure and use OSS metadata caches for evaluation purposes. 

### Enabling & Using Metadata Caching

ProGet uses *metadata providers* to periodically query public repositories. These providers are disabled by default and can be enabled under Admin > OSS Metadata Caching.

When enabled, ProGet will also maintain a cache of metadata from the repository. You can see recent items in the cache under each provider, which can help provide an insight into usage and query volume.

### Compliance Analyzer Metadata Queries

Most metadata queries will occur during the nightly [Compliance Analyzer Scheduled Job](/docs/proget/administration/service#scheduled-jobs), which is responsible for checking packages in feeds and builds for compliance with the [Policy Rules](#compliance-rules) you've defined.

#### Feed Packages

For packages in feeds, ProGet will only query a metadata provider if the package originated from a connector that shares a URL with the provider. This means that if you manually downloaded a package file from NuGet.org, and then manually uploaded it to a feed, then ProGet will not query for updated metadata.

Once metadata has been retrieved from a provider, ProGet will update the package's deprecated, unlisted, and outdated status. This may trigger behavior changes in clients like Visual Studio or `npm` that consume these packages, or cause packages to become Noncompliant or Warn.

ProGet also uses the package's last download date to determine how often to query the metadata provider. For packages downloaded within the past 7 days, the provider will be queried at most daily. Packages downloaded within 30 days will be queried no more than once a week, and all other packages are queried every fifteen days.

#### Build Packages

For packages in [active builds](/docs/proget/sca/builds), ProGet will only query a metadata provider if the package was not found in any feed. This may happen if your CI/build servers don't exclusively use ProGet Feeds or in certain ecosystems (like NuGet) that have "built-in" packages that are never downloaded.

### Custom Metadata Providers

Although metadata providers are designed to work with the official public repositories, (NuGet.org, npmjs.org, etc.), they can also be configured to connect to another repository. To create a custom metadata provider, first enable the default provider, edit it, and then click "create custom provider".

We're not entirely sure what the use case would be (which is why creating them is a bit convoluted), but you may find it helpful to get deprecated, unlisted, or outdated package status from another repository.  Please [let us know](https://forums.inedo.com/) if you find this feature useful, so that we can properly document and support it, as well as add authentication and other options.

## ProGet 2023 and Earlier
Policies & Compliance Rules are a new feature for ProGet 2024, replacing the download blocking rules for [licenses (archive.org)](https://web.archive.org/web/20231210172007/https://docs.inedo.com/docs/proget-sca-licenses) and [vulnerabilities (archive.org)](https://web.archive.org/web/20230927141932/https://docs.inedo.com/docs/proget-sca-vulnerabilities) in which you could implement similar functionality. 

When "Policies & Compliance Rules" is first enabled after updating from ProGet 2023 and earlier, existing license rules and vulnerability assessments will be used to create new policies that you can view, edit, and combine as described below. These policies will then be used to determine if a package download should be blocked.
