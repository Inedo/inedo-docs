---
title: v6.2 Migration
subtitle: BuildMaster v6.2 Migration
keywords: buildmaster, legacy
sequence: 200
show-headings-in-nav: true
---

::: {.attention .analogy}
Because this upgrade is more involved than previous upgrades, special support for it has been included with paid licenses. If you are interested in learning more, use the [Submit Ticket Form](https://my.inedo.com/tickets/new) and put `6.2 UPGRADE` in the "How can we help" section to let us know when you are going to upgrade. We also recommend you include the [Legacy Features Dashboard Report Logs](#detection) in the body of the ticket.
:::

BuildMaster 6.2 is a major, transformative version that removes all [legacy features](#detection) from the product. If you still are using these features, the generally recommended strategy is to install a new instance of BuildMaster 6.2 side-by-side, and migrate applications one at a time to the new system (for installs with legacy deployment plans) or use the [backup/restore functionality](/docs/buildmaster/applications/concepts/backup-restore) to migrate all applications at once.

You can also upgrade directly to v6.2 using the Inedo Hub or installer, but all of following must all be true before it is allowed:

{.docs}
 - existing instance has been upgraded to latest version of v6.1
 - the [Legacy Feature Detector](overview) has been run and has reported no legacy features

Any installations that were first installed from v5.0 or later likely do not have any legacy features in the system, but the detector is still required to be run to ensure this.

## Recommended Upgrade Path {#path data-title="Recommended Path"} 

The recommended upgrade path for older version of BuildMaster depends on the existing version:

{.docs}
 - **For < v5.0** - first upgrade to v4.9.10, then v6.1.[latest], then migrate to 6.2
 - **For >= v5.0** - first upgrade to v6.1.[latest], then migrate to 6.2

## Legacy Features Report {#detection data-title="Legacy Features Report"}

The latest version of BuildMaster v6.1 includes a legacy feature detector that generates a high-level view of legacy features that exist in the existing version. Visit the *Administration* > *Legacy Features Dashboard* page to run the option to run the detector.

Once the detector finishes, the dashboard will show status icons that indicate if a feature is in-use. Select the "View Full Log" option to view a raw text log of all items that will need to be migrated or deleted.

If all the features are denoted OK (ignoring "[Variable Syntax](overview#variable-syntax)", which is always gray since its presence cannot be detected like the other features), then a direct upgrade to v6.2 will be allowed by the installer.

::: {.attention .best-practice}
Note: The legacy features detector will scan all active and deactivated applications.  If it finds a legacy feature in a deactivated application, you will either need to update the deactivated application or purge the deactivated application.
:::