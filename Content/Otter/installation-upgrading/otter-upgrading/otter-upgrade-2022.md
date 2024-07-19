---
title: "Upgrading to Otter 2022"
order: 2
url-slug: "otter-upgrade-2022"
---

:::(Error) (Outdated Documentation: Upgrade to Otter 2022 Not Recommended)
If you are using Otter v3 or earlier, we strongly recommend directly [Upgrading to Otter 2023](/docs/otter-upgrade-2023). This document is only kept around for historic purposes.

:::

Otter 2022 is a major upgrade, and this article provides information about what changed, the impact to your instance, and how to mitigate risk during upgrade.

## Planning for Your Upgrade

Otter 2022 is a major upgrade, and the majority of changes were additive features, platform updates (library upgrades, etc.), and UI changes.

However, there are three key things to keep in mind before upgrading:

1. PowerShell Operation (`PSEnsure`, `PSVerify`, `PSCall`) have a new syntax
2. Custom-built extensions need to be recompiled
3. Hybrid user directories were replaced

You can also rollback to Otter v3 if there are issues without restoring your database.


## New Features in Otter 2022

### New Feature: Expanded Scripting Language Support

Introduced as preview features in v3, Otter v2022 now has first-class support for multiple scripting languages: PowerShell, Python, Windows Batch, and OtterScript.

This will allow you to upload your existing scripts and run them directly across servers you've configured, or call them an OtterScript orchestration.


:::(Warning) (Scripting Risk Mitigation)
Be prepared for OtterScripts that use PowerShell operations (i.e. `PSEnsure`, `PSVerify`, `PSCall`) to have errors, and to fix those by editing the OtterScript.
See the [PowerShell Operation Changes](#powershell-operation-changes) to learn more.
:::

### New Feature: Otter Basic Edition

This is a new edition of Otter that is supported via a new license key in Otter v2022.  Otter Basic and Otter Enterprise have nearly all of the same features. However, Otter Enterprise includes the ability to run as a high-availability cluster, use SAML Sign-on, and automatically detect and remediate configuration drift.


### New Feature: SAML & Security Management Overhaul

We've added some more flexibility for logging-in to Otter and made it lot easier to manage security:

* Improved UI for managing security and users
* Allow multiple user directories to be searched at once
* Integrate with SAML providers like Azure and PingID
* Import/export Permissions and Built-in Users
* Enable multiple login options at once: Built-in Username/passwords, Windows Authentication, SAML Authentication

These changes involved changing some of the login/authentication code, which may lead to bugs/regressions where users cannot log-in or a connected systems (build/CI server) reports authentication errors. 

In addition, if you're using the **Hybrid User Directory** (which allowed you to query multiple user directories), those user directories will become enabled, and you will see an error on the User Directories page if you had one configured. You can safely delete this directory.

:::(Warning) (Security Changes Risk Mitigation)
Be prepared to run the [locked out troubleshooting](/docs/installation/security-ldap-active-directory) in the event you are locked out after upgrade. You will be able to restore security after that. If that doesn't work, you can rollback to Otter v3.
:::

### New Feature: New OtterScript Editor

We've completely rewritten the OtterScript editor. In addition to vastly improved usability, the notable changes are:

* New "statement searcher" for adding statements and operations
* Automatic saving of draft changes, with ability to come back and edit later
* Commit/review changes before finalizing/saving
* Autocomplete (i.e. like IntelliSense) when editing OtterScript in Text Mode

:::(Warning) (New OtterScript Editor Mitigation)
There is virtually no risk with the new code editor, but it may take a little getting used to:
 * The "Add Statement Sidebar" was removed, and users will need to click "+[Statement]" buttons under each block
 * Changes need to be "Committed" to take effect, as they are automatically saved while you edit
:::

## PowerShell Operation Changes
To standardize usage/syntaxes across different scripting languages, PowerShell Operations (`PSEnsure`, `PSVerify`, and `PSCall`) were rewritten and have a slightly different syntax:

* A file extension (`.ps1) needs to be used 
* Script parameters need to be specified as a map within the `Parameters` argument

To minimize the impact on your existing OtterScript-based Scripts, Otter can detect if you're using the old syntax by looking for a file extension. 
* At Runtime, Otter will use the "old" operations 
* In the Visual Editor, Otter will automatically convert to the new style

You can explicitly use the "old" operations specifying `PSEnsure1`, `PSVerify1`, and `PSCall1`.

### Syntax Differences
```
# Old (v3) Syntax
PSEnsure EnsureLocalAdmin
(
    User: $PSCredential(LocalAdminAlex)
);

# New (v3) Syntax
PSEnsure EnsureLocalAdmin.ps1
(
    Parameters: %(User: $PSCredential(LocalAdminRich))
);
```

### Implementation Details

Behind the scenes, these changes required us to create an entirely new set of operations.
 * Old operations: `PSEnsure1`, `PSVerify1`, `PSCall1`
 * New operations: `PSEnsure2`, `PSVerify2`, `PSCall2`

In later releases of Otter v3, `PSEnsure2`, `PSVerify2`, `PSCall2` are available on a preview basis.

In Otter v2022, `PSEnsure`, `PSVerify`, and `PSCall` are aliases for `2`, and the aliases are preferred. This means you should not explicitly use `PSEnsure2`, `PSVerify2`, `PSCall2` in your OtterScript.

## Custom Extensions Changes
Otter 2022 targets `Inedo.SDK-2.0`, which means that you'll need to rebuild any custom extensions you've created. Note that this version of the SDK targets .NET6.

See [creating an extension documentation](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating) to learn more about how to do this.

### Extensions Targeting Otter v1
Otter v1 did not use the Inedo SDK, but instead used the Otter SDK. This means that you'll need to remove the `Inedo.Otter.SDK` NuGet package from your project and then installing the `Inedo.SDK NuGet` package.  

The required code changes are:
* Namespaces – rename namespaces such that any reference to `Inedo.Otter.XXXXX` is changed to `Inedo.XXXXX`. The list of namespaces is found in the SDK Reference.
* `RemoteCollectAsync(IRemoteOperationExecutionContext)` – this method override should be updated to accept `IRemoteOperationCollectionContext` instead.

Additionally, the `DB` class was removed. There is no current workaround for this besides querying the database directly. Common infrastructure and components (e.g. servers, roles, configuration values) should be accessed via the SDK class.

### Extensions Targeting Otter v2 or v3
Otter v2 and v3 targeted different versions of Inedo SDK 1.x. This means that you'll need to update the `Inedo.SDK NuGet` package reference in your project to Inedo-SDK 2.0. 



## Upgrade Process
You can perform the upgrade from the Inedo Hub. Like with all upgrades, you should make sure your [installation is backed-up](/docs/installation/backing-up-restoring).

You can upgrade from any version of Otter to v2022, and there is no need to install intermediate versions. But do note that you can only rollback to Otter 3.0; any earlier, and you'll need to restore your database as well.


## Rolling Back

You can rollback using the Inedo Hub.

:::(Info)
**You can rollback to any version of Otter 3.0 without restoring the database.**
:::

While there are database schema changes, they are all backwards-compatible, which means you can safely rollback your Otter installation if there's a showstopper bug, and then upgrade later.
