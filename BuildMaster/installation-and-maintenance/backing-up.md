---
title: Backing Up BuildMaster
keywords: buildmaster, databases, backup
show-headings-in-nav: true
sequence: 200
---

BuildMaster should be backed up frequently, and as a .NET- and SQL Server-based this is relatively easy to do. There are three things to back up on the BuildMaster server:

{.docs}
- **BuildMaster Database** - a SQL Server database that contains all of BuildMaster's configuration data
- **Encryption key** - the value stored in both the web.config and BuildMaster.Service.config that's used to encrypt/decrypt sensitive data, most notably [resource credentials](/support/documentation/buildmaster/administration/resource-credentials)
- **Shared Configuration** (as of v6.0.7) - the file `%PROGRAMDATA%\Inedo\SharedConfig\BuildMaster.config` contains the encryption key that's used to encrypt/decrypt sensitive data, most notably [resource credentials](/support/documentation/buildmaster/administration/resource-credentials)
- **Artifact Library Files** - a path on disk (defined in Artifacts.BasePath setting) that contains all the files for artifacts you created within BuildMaster

You may also back up your [extensions](/support/documentation/buildmaster/reference/extensions) folder, which is stored in the path defined in the Extensions.ExtensionsPath advanced configuration setting. This will make restoring to a new server as easy as possible, in that you'll just need to copy the backup files to the same location on the new server.

## Database Backup {#database-backup data-title="Database Backup"}

If you've never worked with Microsoft SQL Server before, not to worry - you won't even know you're working with it. But if you're already familiar with Microsoft SQL Server and know how to back up and restore databases, you can skip this section. We're not doing anything special, so you are free to use whichever strategy works in your organization. So long as that strategy involves backing up the database.

If you installed BuildMaster with the built-in database, then the installer also installed Microsoft SQL Server Express 2016 Edition with an instance named `INEDO`. One of the tools installed with SQL Express is the command-line execution <code>osql.exe</code>; it's located in the SQL Server tools directory: `C:\Program Files (x86)\Microsoft SQL Server\130\Tools\Binn`.

You can use a single command to back up the database, which you may need to run as an administrator if you have UAC enabled in Windows.

<pre> osql -S .\INEDO -E -Q "BACKUP DATABASE [BuildMaster] TO DISK=N'C:\Backups\BuildMaster.bak' WITH FORMAT" </pre>

You can obviously customize the path on disk as needed. To run this command on a regular basis, just set up a scheduled task using the [Task Scheduler](http://windows.microsoft.com/en-US/windows-vista/Schedule-a-task). Note that you can back up BuildMaster at any time without having to stop any of the services.

:::attention{.best-practice}
Note: BuildMaster v5 and earlier install SQL Server 2005 Express, and the SQL Express was named **BUILDMASTER**
:::

## Restoring a BuildMaster Installation {#restore data-title="Database Restore"}

There are generally three reasons why you might want to restore a BuildMaster installation from back-up.

{.docs}
- **Rolling back to an earlier version of BuildMaster** - if there were issues with or during the installation of a new version of BuildMaster.
- **Moving BuildMaster to a new server**
- **Undoing a Serious Error**

### The Easy Way (Restore from same version)

The easiest way to restore a BuildMaster installation is to first ensure that you have a working BuildMaster installation that has the same version number of your back-up. That means you'd want to restore a 2.6.1 back-up to a working 2.6.1 installation, for example. If you're not sure what version your back-up is (perhaps your server blew up and you only have the .bak file), then you'll have to use the advanced way described after this section.

If you backed up your database using the command above, then you can use the following command to restore:
<pre>osql -S .\INEDO -E -Q "RESTORE DATABASE [BuildMaster] FROM  DISK = N'C:\Backups\BuildMaster.bak' WITH  FILE = 1,  NOUNLOAD,  STATS = 10"</pre>

Just make sure the BuildMaster Windows Service(s) are stopped, otherwise SQL Server will complain with a "database in use" error.

After you get the database up, just make sure your artifact files are where they are expected to be, and start the service(s).

### The Advanced Way (Restore from any version)

This isn't really that much harder, but it does involve some knowledge of Microsoft SQL Server or the willingness to contact us for assistance. Firstly, you won't be able to restore a newer back-up to an older version (i.e. a 3.0.0 back-up to 2.6.1). But since you should never need to do that anyway, there's nothing to worry about.

If you're using your own SQL Server, then all you need to do is make sure the BuildMaster Database has been restored <em>before</em> running the installer of the version you want. The installer will make sure the restored database is up-to-date as part of the installation.

If you want to use the built-in database, then start by running the installer. It will install the files and create a new BuildMaster database for you. From here, restore your database back-up (you can use the command above) and then run the Database Tool (available from inedo.com) for the version you are installing. That will ensure the database is up to date.
