﻿---
title: Vulnerability Scanning and Blocking
subtitle: Vulnerability Scanning and Blocking
sequence: 100
keywords: proget, vulnerabilities
display-child-topics: false
show-headings-in-nav: true
---

When you incorporate third-party, open-source packages into your applications or build your Docker containers from third-party container images, you also incorporate those packages' and container images' security vulnerabilities. But manually checking for vulnerabilities is an extraordinary drain on resources. 

ProGet automates vulnerability scanning. We partner with three leading vulnerability scanning companies – Sonatype (OSS Index), WhiteSource, and Red Hat (Clair)  – to automatically scan third-party packages and Docker container images against vulnerability databases like the [National Vulnerability Database.](https://nvd.nist.gov/). You can also manually manage your vulnerabilities and can reassess at any time. Container image vulnerability management is available in ProGet 5.3 and later.

:::attention {.best-practice}
This feature is available in paid and trial ProGet editions.
:::

## Vulnerability Scanning and Blocking Workflows {#scanning-and-blocking data-title="Scanning and Blocking Workflows"}

ProGet supports four different workflows for managing vulnerabilities:

|  | [Manual](#manual) | OSS Index | WhiteSource | Clair<sup>*</sup> |
|---|---|---|---|
| Cost                                      | - | - | $ | - |
| Thoroughness                              | ★☆☆ | ★★☆ | ★★★ | ★★☆ |
| Block vulnerable packages                 | ✔ | ✔ | ✔ | - |
| Block vulnerable container images         | ✔ | - | - | ✔ |
| Manually enter vulnerability reports      | ✔ | -  |   | |
| Assess vulnerability report within ProGet | ✔ | ✔ | - | ✔ |
| Automatically scan public databases       | - | ✔ | ✔ | ✔ |
| Scan proprietary databases                | - | - | ✔ | - |
| Requires subscription                     | - | -<sup>R</sup> | [Sign Up](https://www.whitesourcesoftware.com/trial3/)  | - |


<sup>R</sup>While OSS Index doesn't require a paid subscription, users will need to create a [registered account](https://ossindex.sonatype.org/ ).<br/>
<sup>*</sup>New to ProGet 5.3 is support for [Clair](/docs/proget/compliance/clair), which only works with container registries in ProGet.

See [Integrating ProGet with OSS Index](/docs/proget/compliance/vulnerabilities/vor), [Integrating ProGet with WhiteSource](/docs/proget/compliance/whitesource), and [Integrating ProGet with Clair](/docs/proget/compliance/clair) documentation for more details on those workflows.


## Feeds and Vulnerability Configuration {#feed-and-vulnerability data-title="Feeds and Vulnerability Configuration"}

A feed must be explicitly configured to use automated vulnerability scanning and blocking. Manual vulnerability records can be added without any additional setup.  While the end result is the same, the workflows use different features within ProGet:

*   **Automated vulnerability sources and manual vulnerability records** are used for manual, OSS Index, and Clair managed workflows; these add vulnerability records into ProGet that you must assess
*   **Package access rules** are used for WhiteSource-managed workflows; these block package downloads based on rules configured in WhiteSource

You can configure vulnerability sources and package access rules on the Manage Feed page. Note that Clair requires extra setup, the details for which can be found in the [configuring clair in ProGet](/docs/proget/compliance/clair#configureproget) section. If you don't see OSS Index as a vulnerability source or WhiteSource as a package access rule, check Admin > Extensions to make sure those extensions are installed.

:::attention {.best-practice}
Automated vulnerability sources download vulnerabilities and check associated feeds using a scheduled job. 
:::

### Manually Check For Vulnerabilities {#manually-run data-title="Manually Check For Vulnerabilities"}

Automated vulnerability sources can have their scheduled job executed manually at any time by running the `VulnerabilityDownloader` task.  To execute this job, navigate to _Administration -> Scheduled Jobs_ page.  Then click the green play button to the right of the `VulnerabilityDownloader` task.  This is a system-level scoped task that will download vulnerabilities for all of the configured sources and check their associated feeds for vulnerabilities.  



## Vulnerability Reports and Assessments in ProGet{#reports-and-assessments data-title="Reports and Assessments in ProGet"}

Both the manual and OSS Index workflows use vulnerability reports, which essentially identify that a particular package or versioned range of packages has a known vulnerability. This record is either manually entered or is imported from OSS Index, based on packages in a particular feed.

For container reports, Clair attempts to determining which operating system (OS) a container image was built with and then uses that OS to scan specific security databases to check for vulnerabilities. These vulnerabilities are then automatically associated with the container's affected layer within the registry Clair was configured to scan.  Manual vulnerability records can also be added to affected container layers by specifying that layer's digest.

All newly entered or imported vulnerability reports are considered unassessed, which means that packages matching the vulnerability will be blocked until the report is assessed. An assessment involves an authorized user reviewing the report, choosing an assessment type (Ignore, Caution, Block), and leaving an optional comment.

![Assessment workflow](/resources/documentation/proget/assess-workflow.svg)

### Assessment Expiry

An "assessment" you make on a package or container image vulnerability may be configured to expire. This means that, unless it's reassessed, the vulnerability report will be considered "unassessed" after expiry.

There are two primary use-caes for expiration.
 * If you permit packages or container images with unassessed vulnerabilities to be downloaded, this means that you can use an expiring assessment to temporary block a package.
 * If you block unassessed vulnerabilities, then you can use an expiring assessment to temporary un-block a package.

The reason to temporarily block or unblock packages is due to the nature of security vulnerabilities. Just because a particular package or container image has a security vulnerability, it doesn't mean your application will utilize the vulnerable aspects. In fact, constantly jumping to newer versions just to avoid reported vulnerabilities is often less secure - the newer versions have unknown vulnerabilities, which you *might* be using.

By temporary unblocking packages and container images, you can periodically review how engineers are using them. The usage may have changed.


### Manual Vulnerability Records {#manual}

A manual vulnerability record is used to add specific package versions (or [version ranges](#version-ranges)) or which container image layers to block. Manual vulnerability records can be added to a package or container image layer without additional configuration. Visit the `Vulnerabilities` page or the `Vulnerabilities` tab on the package or container image page then click the `Add Vulnerability` button to specify the feed, the package ID and version(s) or the container image layer digest, and the details of the vulnerability.

#### Blocking a Package Download

Selecting `Blocked` will prevent any packages within the version range or container images that include the vulnerable layer to be downloaded. From the `Vulnerabilities` page or the `Vulnerabilities` tab on the package or container image page, selecting the vulnerability, then `Assess`, and choose `Blocked`, optionally adding a comment.

Browsing to a package version within the range specified or a container image with an associated layer in the blocked vulnerability will display `Blocked` where the download button would normally be, and the packagem or container image will be not be downloadable from its associated feed API.

_Note: while the package version or container image is blocked from download, it may still appear in search or list results._

#### Version Ranges 

Manual vulnerability records may encompass multiple package versions using version range syntax, for example:

| Range         | Meaning                                       |
|---            |---                                            |
| 3.0.0	        | version = 3.0.0                               |
| [3.0]     	| version = 3.0                                 |
| <=2.0 	    | version <= 2.0                                |
| [1.3,1.4]	    | 1.3 <= version <= 1.4                         |
| >=1.3 <=1.4   | 1.3 <= version <= 1.4                         |
| >2.5          | version > 2.5                                 |
| <=1.0,>=1.2	| version <= 1.0 or version >= 1.2              |
| <1.1 >1.1 	| Exclude version 1.1                           |

_Note: versions must be specified out to their full value to match. For example, 2.0 will *not* match 2.0.0_

Due to the inconsistency on container repository tags, container registries are not compatible with version ranges.

### Custom Assessment Types

ProGet comes with three built-in assessment types:

*   **Ignore** indicates that the vulnerability report is not applicable or irrelevant and therefore allows for packages to be downloaded
*   **Caution** tells developers to be careful to avoid the vulnerability; packages may be downloaded, but a warning is issued on the web UI
*   **Blocked** means a vulnerability is too severe to allow use, and packages are prevented from being downloaded

You can edit or create your own assessment type, specifying a name, expiry date, severity (color), and whether or not packages would be blocked.
