---
title: Vulnerability Scanning and Blocking
subtitle: Vulnerability Scanning and Blocking
sequence: 100
keywords: proget, vulnerabilities
display-child-topics: false
show-headings-in-nav: true
---

When you incorporate third-party, open-source packages into your applications, or build your Docker containers from third-party container images, you also incorporate those packages' and container images' security vulnerabilities.

Fortunately, security researchers are constantly inspecting these packages and container images, and publishing vulnerabilities they find in public security databases, such as the [National Vulnerability Database.](https://nvd.nist.gov/) This allows both authors and consumers to fix and patch as needed.

But *unfortunately*, managing vulnerabilities as a consumer is challenging, especially when that "consumer" is an enterprise. Manually checking each third-party package or container image against the National Vulnerability Database would be a full-time job for a full-time team (an extraordinary drain on resources). And even if you were to carefully assess each third-party package or container image for known vulnerabilities before allowing its use in your organization, new security vulnerabilities are discovered all the time, which would mean constantly assessing and reassessing.

This is where ProGet's vulnerability scanning and blocking comes in.

:::attention {.best-practice}
This feature is available in paid and trial ProGet editions. [Learn about ProGet free trials and get a committment-free 30-day trial](https://inedo.com/proget/pricing/trial).
:::

We partner with three leading vulnerability scanning companies – Sonatype (OSS Index), WhiteSource, and Red Hat (Clair)  – to automatically scan third-party packages and Docker container images against vulnerability databases. You can also manually manage your vulnerabilities. Note that container image vulnerability management is available in ProGet 5.3 and later.

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

A feed must be explicitly configured to use vulnerability scanning and blocking. While the end result is the same, the workflows use different features within ProGet:

*   **Vulnerability sources** are used for manual, OSS Index, and Clair managed workflows; these add vulnerability records into ProGet that you must assess
*   **Package access rules** are used for WhiteSource-managed workflows; these block package downloads based on rules configured in WhiteSource

You can configure both on the Manage Feed page. Note that Clair requires extra setup, the details for which can be found in the [configuring clair in ProGet](/docs/proget/compliance/clair#configureproget) section. If you don't see OSS Index as a vulnerability source or WhiteSource as a package access rule, check Admin > Extensions to make sure those extensions are installed.

:::attention {.best-practice}
Vulnerabilities are downloaded with a scheduled job. 
:::

## Vulnerability Reports and Assessments in ProGet{#reports-and-assessments data-title="Reports and Assessments in ProGet"}

Both the manual and OSS Index workflows use vulnerability reports, which essentially identify that a particular package or versioned range of packages has a known vulnerability. This record is either manually entered or is imported from OSS Index, based on packages in a particular feed.

For container reports, Clair attempts to determining which operating system (OS) a container image was built with and then uses that OS to scan specific security databases to check for vulnerabilities. These vulnerabilities are then automatically associated with the container's affected layer within the registry Clair was configured to scan.

All newly entered or imported vulnerability reports are considered unassessed, which means that packages matching the vulnerability will be blocked until the report is assessed. An assessment involves an authorized user reviewing the report, choosing an assessment type (Ignore, Caution, Block), and leaving an optional comment.

![Assessment workflow](/resources/documentation/proget/assess-workflow.svg)

### Assessment Expiry

An "assessment" you make on a package or container image vulnerability may be configured to expire. This means that, unless it's reassessed, the vulnerability report will be considered "unassessed" after expiry.

There are two primary use-caes for expiration.
 * If you permit packages or container images with unassessed vulnerabilities to be downloaded, this means that you can use an expiring assessment to temporary block a package.
 * If you block unassessed vulnerabilities, then you can use an expiring assessment to temporary un-block a package.

The reason to temporarily block or unblock packages is due to the nature of security vulnerabilities. Just because a particular package or container image has a security vulnerability, it doesn't mean your application will utilize the vulnerable aspects. In fact, constantly jumping to newer versions just to avoid reported vulnerabilities is often less secure - the newer versions have unknown vulnerabilities, which you *might* be using.

By temporary unblocking packages and container images, you can periodically review how engineers are using them. The usage may have changed.


### Manual Vulnerability Source {#manual}

A manual vulnerability source is used to add specific package versions (or [version ranges](#version-ranges)) or which container image layers to block. Visit the `Administration > Components & Extensibility > Vulnerability Sources` page to create a manual vulnerability source. Once created, visit the `Compliance > Vulnerabilities` page and click `Add Vulnerability` to specify the package ID and version(s) or the container image layer digest and the details of the vulnerability.

#### Blocking a Package Download

Selecting `Blocked` will prevent any packages within the version range or container images that include the vulnerable layer to be downloaded. From the `Compliance > Vulnerabilities` page, selecting the vulnerability, then `Assess`, and choose `Blocked`, optionally adding a comment.

In order for this assessment to take effect, it must be associated with a feed. On the `Manage Feed` page, select `add source` from the Vulnerability Sources panel and add the desired source to the feed. Browsing to a package version within the range specified or a container image with an associated layer in the blocked vulnerability will display `Blocked` where the download button would normally be, and the package will be not be downloadable from its associated feed API.

_Note: while the package version is blocked from download, it may still appear in search or list results._

#### Version Ranges 

Manual vulnerability sources may encompass multiple package versions using version range syntax, for example:

| Range         | Meaning                                       |
|---            |---                                            |
| 3.0.0	        | version = 3.0.0                               |
| [3.0]     	| version = 3.0                                 |
| (,2.0]	    | version <= 2.0                                |
| [1.3,1.4]	    | 1.3 <= version <= 1.4                         |
| [1.0,2.0)     | 1.0 <= version < 2.0                          |
| [2.5)         | version > 2.5                                 |
| (,1.0],[1.2,)	| version <= 1.0 or version >= 1.2              |
| (,1.1),(1.1,)	| Exclude version 1.1                           |

_Note: versions must be specified out to their full value to match. For example, 2.0 will *not* match 2.0.0_

Due to the inconsistency on container repository tags, container registries are not compatible with version ranges.

### Custom Assessment Types

ProGet comes with three built-in assessment types:

*   **Ignore** indicates that the vulnerability report is not applicable or irrelevant and therefore allows for packages to be downloaded
*   **Caution** tells developers to be careful to avoid the vulnerability; packages may be downloaded, but a warning is issued on the web UI
*   **Blocked** means a vulnerability is too severe to allow use, and packages are prevented from being downloaded

You can edit or create your own assessment type, specifying a name, expiry date, severity (color), and whether or not packages would be blocked.
