---
title: Backing Up Otter
keywords: otter,databases,backup
show-headings-in-nav: true
sequence: 200
---


There are three components to back up on the Otter server:

{.docs}
- **Otter Database** - a SQL Server database that contains all of Otter's configuration data; this is absolutely vital to back-up
- **Shared Configuration** - the file `%PROGRAMDATA%\Inedo\SharedConfig\Otter.config` contains the encryption key that's used to encrypt/decrypt sensitive data, most notably [resource credentials](/support/documentation/buildmaster/global-components/resource-credentials)
- **Non-database Rafts** - any [rafts](../global-components/rafts) you created that aren't otherwise backed-up

## Backing-up the Otter Database {#backing-up-database data-title="Database Backup"}

If you've never worked with Microsoft SQL Server before, not to worry - you won't even know you're working with it. But if you're already familiar with Microsoft SQL Server and know how to back-up and restore databases, you can skip this section. We're not doing anything special, so you are free to use whichever strategy works in your organization.

If you installed Otter with the built-in database, then the installer also installed Microsoft SQL Server Express 2016 Edition with an instance named INEDO. One of the tools installed with SQL Express is the command-line execution `osql.exe`; it's located in the SQL Server tools directory: `C:\Program Files (x86)\Microsoft SQL Server\130\Tools\Binn`.

You can use a single command to back up the database, which you may need to run as an administrator if you have UAC enabled in Windows.

```
osql -S .\INEDO -E -Q "BACKUP DATABASE [Otter] TO DISK=N'C:\Backups\Otter.bak' WITH FORMAT"
```

You can obviously customize the path on disk as needed. To run this command on a regular basis, just set up a scheduled task using the [Task Scheduler](http://windows.microsoft.com/en-US/windows-vista/Schedule-a-task). Note that you can back up Otter at any time without having to stop any of the services.

:::attention {.best-practice}
Note: Otter v1 installed SQL Server 2005 Express, and the SQL Express instance was named **OTTER**
:::

## Restoring an Otter Installation {#restoring data-title="Database Restore"}

There's generally three reasons why you might want to restore an Otter installation from back-up.

{.docs}
- **Rolling-back to an earlier version of Otter** - if there were issues with or during the installation of a new version of Otter.
- **Moving Otter to a new server**
- **Undoing a Serious Error**

### The Easy Way (Restore from same version) {#easy-way}

The easiest way to restore an Otter installation is to first ensure that you have a working Otter installation that has the same version number of your back-up. That means you'd want to restore a 2.6.1 back-up to a working 2.6.1 installation, etc. If you're not sure what version your back-up is (perhaps your server blew up and you only have the .bak file), then you'll have to use the advanced way described after this section.

If you backed-up your database using the command above, then you can use the following command to restore:

```
osql -S .\INEDO -E -Q "RESTORE DATABASE [Otter] FROM  DISK = N'C:\Backups\Otter.bak' WITH  FILE = 1,  NOUNLOAD,  STATS = 10"
```

Just make sure the Otter Windows Service(s) are stopped, otherwise SQL Server will complain with a  "database in use" error.

### The Advanced Way (Restore from any version) {#advanced-way}

This isn't really that much harder, but it does involve some knowledge of Microsoft SQL Server or the willingness to [contact us](/support/ticket) for assistance. Firstly, you won't be able to restore a newer back-up to an older version (i.e. a 3.0.0 back-up to 2.6.1). But since you should never need to do that anyway, there's nothing to worry about.

If you're using your own SQL Server, then all you need to do is make sure the Otter Database has been restored *before* running the installer of the version you want. The installer will make sure the restored database is up-to-date as part of the installation.

If you want to use the built-in database, then start by running the installer. It will install the files and create a new Otter database for you. From here, restore your database back-up (you can use the command above) and then run the database change script updater (from the manual installation package) for the version you are installing. That will ensure the database is up-to-date.
