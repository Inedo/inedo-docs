---
title: "Manually Upgrade an Existing Installation"
order: 4
---

::: (WARNING) (Note)
   We do not support manual installations, and this document is for informational and troubleshooting purposes only. If you really want to install the product without using Inedo Hub, you can follow these instructions to do so — but your successor may have a very difficult time maintainng this installation.
:::

Upgrading an Inedo product installation from an existing installation can be performed by following this installation guide, except it should be much simpler and quicker than the initial installation since the infrastructure is already in place.

This guide may also be used to upgrade an instance that was installed by the installer, but once that has occurred, this manual process must be used going forward.

### 1. Backup the existing instance
Before upgrading your Inedo product, please make sure to backup your instance.  This will allow you to rollback to your previous version in case any issues are encountered during the upgrade process.  To backup the instance, refer to our [backing up and restoring guide](/docs/installation/backing-up-restoring).

### 2. Stop services
Before upgrading your Inedo product, the existing instance must be taken offline. Ensure that the application pool and Windows Service are both stopped.

Here is an example PowerShell script that performs these operations:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" stop apppool «Inedo-product»
Stop-Service INEDO«Inedo-product»SVC
```

### 3. Copy installation package contents
For the most part, an upgrade is an "XCOPY deployment" of files from the [manual installation package](/docs/installation/manual-installation#get). Copy all of the contents of each of the following into the specified target directory while carefully analyzing the differences in configuration files as noted:

**BuildMaster**

| Contents | Target | Installer Default | Notes |
| --- | --- | --- | --- |
| Service | Service installation directory | `C:\Program Files\BuildMaster\Service` | May overwrite existing `App_appSettings.config` if empty |
| Web | Website application home directory | `C:\Program Files\BuildMaster\Web` | May overwrite existing `Web_appSettings.config` if empty |
| Extensions | `Extensions.BuiltInExtensionPath` directory | `C:\Program Files\BuildMaster\Extensions` | Overwrite contents with install package contents |

While v6.0.7 and later versions of BuildMaster should not store data in these configuration files, existing installations might contain relevant data such as connection strings that should ultimately be migrated to the [BuildMaster Configuration File](/docs/installation/configuration-files) and removed from the `*_appSettings.config` file.

**ProGet**

| Contents | Target | Installer Default | Notes |
| --- | --- | --- | --- |
| Service | Service installation directory | `C:\Program Files\ProGet\Service` | May overwrite existing `App_appSettings.config` if empty |
| Web | Website application home directory | `C:\Program Files\ProGet\Web` | May overwrite existing `Web_appSettings.config` if empty |
| Extensions | `Extensions.BuiltInExtensionPath` directory | `C:\Program Files\ProGet\Extensions` | Overwrite contents with install package contents |

While v5.1.0 and later versions of ProGet should not store data in these configuration files, existing installations might contain relevant data such as connection strings that should ultimately be migrated to the [ProGet Configuration File](/docs/installation/configuration-files) and removed from the `*_appSettings.config` file.

**Otter**

| Contents | Target | Installer Default | Notes |
|---|---|---| --- |
| Service | Service installation directory | `C:\Program Files\Otter\Service` | May overwrite existing `App_appSettings.config` if empty |
| Web | Website application home directory | `C:\Program Files\Otter\Web` | May overwrite existing `Web_appSettings.config` if empty |
| Extensions | `Extensions.BuiltInExtensionPath` directory | `C:\Program Files\Otter\Extensions` | Overwrite contents with install package contents |


### 4. Update database schema
Because the tool used to update the database schema is aware of previous scripts that have run, this process is the same as the initial installation process. See the [Database Installation](/docs/installation/manual-installation#update-database-schema) from the [manual installation guide](/docs/installation/manual-installation) to perform this step using the existing connection string specified in the Inedo product configuration file. An additional benefit of being aware of previous scripts that have run is that the `inedosql.exe` command can be applied to a database copy or backup to test the SQL upgrade before running it against a production database.
•	[BuildMaster Configuration File](/docs/installation/configuration-files)
•	[ProGet Configuration File](/docs/installation/configuration-files)
•	[Otter Configuration File](/docs/installation/configuration-files)

### 5. Start services
Once all files are copied and the database is updated, the services can be started to bring your Inedo product back online.

Here is an example PowerShell script that performs these operations for ProGet:
```
&"C:\Windows\System32\inetsrv\appcmd.exe" start apppool «Inedo-product»
Start-Service INEDO«Inedo-product»VC
```