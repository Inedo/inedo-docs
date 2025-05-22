---
title: "Backing Up & Restoring"
order: 11
---

Your Inedo products should be backed up frequently. 

For smaller, single-server installations, you may find it easiest to simply take a snapshot of the Virtual Machine the product is running on. As long as all components are hosted on a single virtual machine, this will sufficiently backup the product.

However, this isn't possible or practical for many installations, and this article will give advice on how to create a back-up plan. 

### Components to Backup

These items are critical data you should back up for each product:

| Item | Note |
| - | - |
| [Configuration&nbsp;File](#installation-configuration-file-backup) <br/>*(Windows&nbsp;only)*  | the installation configuration file that contains your database connection string and the encryption key that is used to encrypt secrets such as passwords |
| [Database](#database-backup) | contains all of your Inedo product's configuration data and requires special consideration |
| [Package&nbsp;Files](#package-files-backup) <br/>*(ProGet&nbsp;only)* | the disk paths that contain all the files for packages stored in ProGet |
| [Artifact&nbsp;Files](#artifact-files-backup)<br />*(BuildMaster&nbsp;only)* | a path on disk that contains all the files for artifacts you created within BuildMaster |

You can also back up your extensions folder (plug-ins), which is stored in the path defined in the `Extensions.ExtensionsPath` advanced configuration setting. This makes restoring to a new server as easy as possible, since you only need to copy the backup files to the same location on the new server.


##  Configuration File Backup {#installation-configuration-file-backup}

On Windows-based installations, the [installation configuration file](/docs/installation/configuration-files) contains sensitive information, so it's important to consider how (or even *if*) you want to back this file up.

The most important piece of information in this file is the `EncryptionKey` setting. This is used to decrypt secrets stored in the database, such as passwords for connectors in ProGet or agent credentials in BuildMaster and Otter. Without the encryption key, your Inedo product will not be able to decrypt these secrets and unexpected errors may occur.

Also, someone who has access to the database and the encryption key could decrypt the secrets, so you should be careful where you back up the file. This file also contains your database connection string, which may include a password to connect to the database.


## Database Backup

Inedo products are constantly reading/writing from the database, which means that you can't simply copy the database files. Instead, you'll need to follow a special process, depending on the database engine in use.

### SQL Server
If you installed your Inedo product with the built-in database, then the installer also installed Microsoft SQL Server Express Edition with an instance named `INEDO`. The database is relatively easy to back-up using <code>osql.exe</code> or SQL Server Management Studio (SSMS). 

To learn more about how to use these tools, see our guide on [Backing Up and Restoring Databases](/docs/installation/sql-server/installation-database-backup).

### PostgreSQL (ProGet 2025+ Only)

<div style="background-color:#FEECE5;padding:4px;border:solid 1px #0FECA1;">

ProGet 2025+ can use PostgreSQL instead of SQL Server. The database is relatively easy to back-up using the built-in `inedodb backup` CLI command. See [PostgreSQL Database Backup and Restore](/docs/installation/postgresql#backup) to learn more.

</div>

## Package Files Backup
::: (warning)
This only applies to ProGet
:::

All package files should be backed up regularly.

By default, a feed's package files are stored on disk in the `%ProgramData%\ProGet` directory. However, this directory can be changed by either editing the default directory or  setting the Disk Path property on a feed. See [File Storage and Drop Paths](/docs/proget/feeds/feed-overview/proget-feed-storage) to learn more.


## Artifact Files Backup
::: (warning)
This only applies to Buildmaster
:::
Backup copies of all artifact files should be made regularly.

By default, artifacts are stored in a location (defined in `Artifacts.Base.Path settings`) that contains all files for artifacts you have created in BuildMaster.


## Restoring an Installation 
There are generally three reasons why you might want to restore an Inedo product installation from a backup.
- **Rolling back to an earlier version of your Inedo product** - if there were issues with or during the installation of a new version of the product
- **Moving your Inedo product to a new server** - see our guide for [migrating BuildMaster to a new server](/docs/buildmaster/installation-maintenance/buildmaster-migrating-instance-to-new-server)
- **Undoing a Serious Error** - such as making a mistake in the software itself


### The Easy Way (Restore from the same version)
The easiest way to restore an Inedo product installation is to first make sure you have a working installation with the same version number as your backup. This means that you should restore a backup of ProGet 2.6.1 to a working 2.6.1 installation, and so on.

If you aren't sure what version your backup has (maybe your server broke and you have only the .bak file), then you must use the advanced method described after this section.

If you backed up your database with the above command, you can use the following command to restore it:

| Product | Restore command |
| ---| --- |
| ProGet | `osql -S .\INEDO -E -Q "RESTORE DATABASE [ProGet] FROM  DISK = N'C:\Backups\ProGet.bak' WITH  FILE = 1,  NOUNLOAD,  STATS = 10"` |
| BuildMaster | `osql -S .\INEDO -E -Q "RESTORE DATABASE [BuildMaster] FROM  DISK = N'C:\Backups\BuildMaster.bak' WITH  FILE = 1,  NOUNLOAD,  STATS = 10"` |
| Otter | `osql -S .\INEDO -E -Q "RESTORE DATABASE [Otter] FROM  DISK = N'C:\Backups\Otter.bak' WITH  FILE = 1,  NOUNLOAD,  STATS = 10"` |

Just make sure that the Windows services of the Inedo product are stopped; otherwise SQL Server will report the error "Database in use".

After you have set up the database, make sure your artifact files (BuildMaster) and/or package files (ProGet) are where they should be and start the service(s).

### The Advanced Way (Restore from any version)
This isn't really much harder, but it does require some knowledge of Microsoft SQL Server or a willingness to ask us for help. First, you cannot restore a newer backup to an older version (for example, a backup from BuildMaster 3.0.0 to 2.6.1). However, since you never need to do this anyway, there is no need to worry.

If you're using your own SQL server, you just need to make sure that your Inedo product's database has been restored before running the installer for the version you want. The installer will make sure that the restored database is up-to-date as part of the installation.

If you want to use the built-in database, run the installer first. It'll install the files and create a new Inedo product database for you. From here, restore your database backup (you can use the command above) and then run the database tool (available at inedo.com) for the version you're installing. This will ensure that the database is up to date.