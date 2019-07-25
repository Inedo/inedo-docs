---
title: Vulnerability Scanning and Blocking
subtitle: Vulnerability Scanning and Blocking
sequence: 100
keywords: proget, vulnerabilities
display-child-topics: false
show-headings-in-nav: true
---

When you incorporate third-party, open-source packages into your applications, you also incorporate those packages' security vulnerabilities.

Fortunately, security researchers are constantly inspecting these packages and publishing vulnerabilities they find in public security databases, such as the [National Vulnerability Database.](https://nvd.nist.gov/) This allows both package authors and consumers to fix and patch as needed.

Unfortunately, managing vulnerabilities as a consumer is challenging, especially as an enterprise. Even if you were to carefully assess each third-party package for known vulnerabilities before allowing its use in applications, new security vulnerabilities are discovered all the time. This means you would need to constantly reassess all third-party packages for new security vulnerabilities.

This is where ProGet's vulnerability scanning and blocking comes in.

:::attention {.best-practice}
This feature is available in paid and trial ProGet editions.
:::
We partner with two leading vulnerability scanning companies – Sonatype (OSS Index) and WhiteSource – to automatically scan third-party packages against vulnerability databases. You can also manually manage your vulnerabilities.

## Vulnerability Scanning and Blocking Workflows {#scanning-and-blocking data-title="Scanning and Blocking Workflows"}

ProGet supports three different workflows for managing vulnerabilities:

|  | [Manual](#manual) | OSS Index | WhiteSource |
|---|---|---|---|
| Cost                                      | - | - | $ |
| Thoroughness                              | ★☆☆ | ★★☆ | ★★★ |
| Block vulnerable packages                 | ✔ | ✔ | ✔ |
| Manually enter vulnerability reports      | ✔ | -  |   |
| Assess vulnerability report within ProGet | ✔ | ✔ | - |
| Automatically scan public databases       | - | ✔ | ✔ |
| Scan proprietary databases                | - | - | ✔ |
| Requires subscription                     | - | -<sup>R</sup> | [Sign Up](https://www.whitesourcesoftware.com/trial3/)  |


<sup>R</sup>While OSS Index doesn't require a paid subscription, users will need to create a [registered account](https://ossindex.sonatype.org/ ).

See [Integrating ProGet with OSS Index](/docs/proget/compliance/vulnerabilities/vor) and [Integrating ProGet with WhiteSource](/docs/proget/compliance/whitesource) documentation for more details on those workflows.


## Feeds and Vulnerability Configuration {#feed-and-vulnerability data-title="Feeds and Vulnerability Configuration"}

A feed must be explicitly configured to use vulnerability scanning and blocking. While the end result is the same, the workflows use different features within ProGet:

*   **Vulnerability sources** are used for manually-and OSS Index- managed workflows; these add vulnerability records into ProGet which you must assess
*   **Package access rules** are used for WhiteSource-managed workflows; these block package downloads based on rules configured in WhiteSource

You can configure both on the Manage Feed page. If you don't see OSS Index as a vulnerability source, or WhiteSource as a package access rule, check Admin > Extensions to make sure those extensions are installed.

:::attention {.best-practice}
Vulnerabilities are downloaded with a scheduled job. 
:::

## Vulnerability Reports and Assessments in ProGet{#reports-and-assessments data-title="Reports and Assessments in ProGet"}

Both the manual and OSS Index workflows use vulnerability reports, which essentially identify that a particular package, or versioned range of packages, has a known vulnerability. This record is either manually entered, or is imported from OSS Index, based on packages in a particular feed.

All newly entered or imported vulnerability reports are considered unassessed, which means that packages matching the vulnerability will be blocked until the report is assessed. An assessment involves an authorized user reviewing the report, choosing an assessment type (Ignore, Caution, Block), and leaving an optional comment.

![Assessment workflow](/resources/documentation/proget/assess-workflow.svg)

### Assessment Expiry

Depending on the assessment type, the assessment may expire; this means that, unless it's reassessed, the vulnerability report will be considered unassessed after expiry.

This can be useful to temporarily allow a package, or to review usage of packages after a certain amount of time.

### Manual Vulnerability Source {#manual}

A manual vulnerability source is used to add specific package versions (or [version ranges](#version-ranges)) to be blocked. Visit the `Administration > Components & Extensibility > Vulnerability Sources` page to create one. Once created, visit the `Compliance > Vulnerabilities` page and click `Add Vulnerability` to specify the package ID and version(s) and the details of the vulnerability.

#### Blocking a Package Download

Once a vulnerability is added,  it must be assessed. Selecting `Blocked` will prevent any packages within the version range to be downloaded. From the `Compliance > Vulnerabilities` page, selecting the vulnerability, then `Assess`, and choose `Blocked`, optionally adding a comment.

In order for this assessment to take effect, it must be associated with a feed. To accomplish this, on the `Manage Feed` page, select `add source` from the Vulnerability Sources panel and add the desired source to the feed. Browsing to a package version within the range specified in the blocked vulnerability will display `Blocked` where the download button would normally be, and the package will be not be downloadable from its associated feed API.

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

### Custom Assessment Types

ProGet comes with three built-in assessment types:

*   **Ignore**; indicates that the vulnerability report is not applicable or irrelevant, and therefore allows for packages to be downloaded
*   **Caution**; developers should be careful to avoid the vulnerability; packages may be downloaded, but a warning is issued on the web UI
*   **Blocked**; vulnerability is too severe to allow use, and packages are prevented from being downloaded

You can edit or create your own assessment type, specifying a name, expiry date, severity (color), and whether or not packages would be blocked.