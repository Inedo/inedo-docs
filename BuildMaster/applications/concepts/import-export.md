---
title: Advanced Import & Export
sequence: 400
show-headings-in-nav: true
---

Behind the scenes, the [Application Template](templates) and [Backup & Restore](backup-restore) features use the application import and export feature.

## Advanced Application Export {#exporting data-title="Advanced Export"}

In order to perform an advanced export, visit the *Settings* > *Advanced Settings* page from within an application. Selecting *Export as Application Template...* > *Advanced...* will enable an application export with the following options:

{.docs}
 - **Compatibility** - used by application templates to coordinate BuildMaster version and export version compatibility. This option is generally only used by Inedo for samples and templates, and otherwise should remain set at "Current"
 - **Package name** - the name of the exported package
 - **Package tags** - optional package tags used primarily to categorize exported application templates and samples
 - **Title** - the UI-friendly title of the exported package
 - **Description** - a more in-depth description of the exported package; Markdown is supported in this field
 - **Icon** - an optional package icon
 - **Options** - currently only a single option to include history (build/release/deployment logs, etc.)

Packages exported from this page will be published to the Templates [package source](/docs/buildmaster/ci-cd/continuous-integration/packaging/package-and-container-sources).

## Importing Applications {#importing data-title="Importing Applications"}

Follow the [Restore Applications](backup-restore#restoring-an-application) documentation to restore an application exported in the previous section.

## Package File Format {#package-file-formate data-title="Package File Format"}

Applications will be exported as a standard [universal package](/docs/proget/core-concepts/packages) which is essentially a zip file containing application configuration and history, along with a JSON-based manifest file (`upack.json`) that describes the contents of the package.

In addition to the standard name and version properties, BuildMaster will include `a _exportDate` and `_bmVersion` property in `upack.json`. The package contents will be a collection of JSON-formatted files including:

| Filename | Description |
| -------- | ----------- |
| **app.json** | Information about the application |
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
| **credentials.json** | Application-scoped credentials, [encryption info](/docs/buildmaster/installation-and-maintenance/config-files) |
| **secure-resources.json** | Application-scoped resources |
| **repository-monitors.json** | Application-scoped repository monitors |
| **webhooks.json** | Application-scoped webhook monitors |
| **counts.json** | Index/counts of all of the items in the import package |
| **setup-template.json** | The corresponding setup template |
| **releases.json** | Historic; releases |
| **builds.json** | Historic; builds |
| **artifacts.json** | Historic; build artifact metadata |
| **promotions.json** | Historic; build deployments to stages |
| **executions.json** | Historic; execution records |
| **log-scopes.json** | Historic; log "scopes" (headings) in log files |
| **log-entries.json** | Historic; log text entries within a scope |

This is not an exhaustive list of files, and because these files are intended to only be used by BuildMaster, the precise format of these files is documented only in the source code (but you can [request source code access](https://inedo.com/contact)).
