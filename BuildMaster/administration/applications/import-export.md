---
title: Importing and Exporting Applications
subtitle: Importing & Exporting Applications
keywords: buildmaster
sequence: 60
show-headings-in-nav: true
---

BuildMaster can export an application's configuration and history into a [universal package](/upack), and then publish that package to a feed in [ProGet](/proget) or a file on a disk. You import applications in the same manner.

## Exporting Applications {#exporting data-title="Exporting Applications"}

You can open the application export page from the application settings, or from Admin > Export Applications. When exporting applications, you have the following options:

{.docs}
- **Applications** - list of applications to export; this option will only appear when the page is accessed from the administration section
- **Package name** - optional; the name of the universal package to create; this defaults to the application name, and is ignored if multiple applications are selected
- **Package version** - optional, the version of the universal package to create; this defaults to 0.0.0
- **Include history** - when selected, the release history and deployment logs will be included in the package. Note that this does not include artifacts

You will also select how to publish the package, from one of two options:

{.docs}
- **Publish to Universal Feed** - a feed on a ProGet instance
- **Save to Disk Path** - a local or network path that the BuildMaster service can write to

Before publishing to a feed, you will need to setup an Inedo Product [resource credential](/support/documentation/buildmaster/administration/resource-credentials) with the URL and optionally an API key to your ProGet server.

## Importing Applications {#importing data-title="Importing Applications"}

You can open the application import dialog form the create new application page, or from Admin > Import Application. When importing, you must first select a source package:

{.docs}
- **Import from Universal Feed** - import a specific package and version from a ProGet feed
- **Load from Disk Path** -a local or network path that the BuildMaster service can read from

You will also be presented with the following options:

{.docs}
- **Application name** - optional; the name of the application to create, this will default to the package name
- **Import history** - when set, any of the release and deployment history that is included in the package will also be imported

Before importing from a feed, you will need to setup an Inedo Product resource credential with the URL and optionally an API key to your ProGet server.

## Package File Format {#package-file-formate data-title="Package File Format"}

Applications will be exported as a standard [universal package](/support/documentation/proget/core-concepts/packages) which is essentially a zip file containing application configuration and history, along with a JSON-based manifest file (`upack.json`) that describes the contents of the package.

In addition to the standard name and version properties, BuildMaster will include `a _exportDate` and `_bmVersion` property in `upack.json`. The package contents will be a collection of JSON-formatted files:

| Filename | Description |
| -------- | ----------- |
| **deployables.json** | Deployables and deployable variables |
| **privileges.json** | Application-scoped privileges |
| **pipelines.json** | Pipelines |
| **global/pipelines.json** | Global pipelines that the application uses |
| **release-templates.json** | Release templates |
| **scripts.json** | Script assets |
| **text-templates.json** | Text template assets |
| **database-change-scripts.json** | Database change scripts |
| **config-files.json** | Configuration file assets |
| **issue-sources.json** | Issue sources configured for the application |
| **issues.json** | Already synchronized issues and their status |
| **plans.json** | Deployment plans |
| **counts.json** | Index/counts of all of the items in the import package |
| **releases.json** | Historic; releases |
| **builds.json** | Historic; builds |
| **promotions.json** | Historic; build deployments to stages |
| **executions.json** | Historic; execution records |
| **log-scopes.json** | Historic; log "scopes" (headings) in log files |
| **log-entries.json** | Historic; log text entries within a scope |

Because these files are intended to only be used by BuildMaster, the precise format of these files is documented only in the source code (but you can [request source code access](/contact)).
