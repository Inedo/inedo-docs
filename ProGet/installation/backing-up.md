---
title: Backing Up & Restoring
sequence: 200
keywords: proget, databases, backup
show-headings-in-nav: true
---

As the home for all of your packages - ProGet should be backed-up frequently. Fortunately, this is relatively easy to do.

There are two things to back-up on the ProGet server:

{.docs}
- **Packages** - disk paths that contain all the files for packages stored in ProGet; this is the most important thing to back-up.
- **ProGet Database** - a SQL Server database that contains all of ProGet's configuration data; this is pretty important to back-up too.
- **Shared Configuration *(v5.1 and later)*** - the file ``%PROGRAMDATA%\Inedo\SharedConfig\ProGet.config``

## Backing-up the ProGet Database {#backup-proget data-title="Backing-up the ProGet Database"}

If you installed ProGet with the built-in database, then the installer also installed Microsoft SQL Server Express 2016 Edition with an instance named INEDO. One of the tools installed with SQL Express is the command-line execution <code>osql.exe</code>; it's located in the SQL Server tools directory: `C:\Program Files (x86)\Microsoft SQL Server\130\Tools\Binn`.

You can use a single command to back up the database, which you may need to run as an administrator if you have UAC enabled in Windows.

```
<osql -S .\INEDO -E -Q "BACKUP DATABASE [ProGet] TO DISK=N'C:\Backups\ProGet.bak' WITH FORMAT">
```

You can obviously customize the path on disk as needed. To run this command on a regular basis, just set up a scheduled task using the [Task Scheduler](http://windows.microsoft.com/en-US/windows-vista/Schedule-a-task). Note that you can back up ProGet at any time without having to stop any of the services.

::: attention {.best-practice}
Note: ProGet v3 installed SQL Server 2005 Express, and the SQL Express was named **PROGET**
:::

## Backing-up the Packages {#packages data-title="Backing-up the Packages"}

By default, packages for feeds are stored in the %ProgramData%\ProGet, but this can be changed at the system- or feed-level, so if you changed those paths, make sure to back up from there instead.         Additionally, if a custom package store is configured, packages may stored in AWS S3 or Azure.

## Restoring a ProGet Installation {#restore data-title="Restoring a ProGet Installation"}

There's generally three reasons why you might want to restore a ProGet installation from back-up.

{.docs}
- **Rolling-back to an earlier version of ProGet** - if there were issues with or during the installation of a new version of ProGet.
- **Moving ProGet to a new server**
- **Undoing a Serious Error**

### The Easy Way (Restore from same version) {#same-version data-title="The Easy Way (Restore from the same version)"}

The easiest way to restore a ProGet installation is to first ensure that you have a working ProGet installation that has the same version number of your back-up. That means you'd want to restore a 2.6.1 back-up to a working 2.6.1 installation, etc. If you're not sure what version your back-up is (perhaps your server blew up and you only have the .bak file), then you'll have to use the advanced way described after this section.

If you backed-up your database using the command above, then you can use the following command to restore:

```
<osql -S .\INEDO -E -Q "RESTORE DATABASE [ProGet] FROM  DISK = N'C:\Backups\ProGet.bak' WITH  FILE = 1,  NOUNLOAD,  STATS = 10">
```

::: attention {.best-practice}
Just make sure the ProGet Windows Service is stopped, otherwise SQL Server will complain with a "database in use" error. **Don't start the service until after you've moved the packages to where they belong.**
:::

After you get the database up, just make sure your package files are where they are expected to be, and start the service.

### The Advanced Way (Restore from any version) {#any-version data-title="The Advanced Way (Restore from any version)"}

This isn't really that much harder, but it does involve some knowledge of Microsoft SQL Server or the willingness to contact us for assistance. Firstly, you won't be able to restore a newer back-up to an older version (i.e. a 3.0.0 back-up to 2.6.1). But since you should never need to do that anyway, there's nothing to worry about.

If you're using your own SQL Server, then all you need to do is make sure the ProGet Database has been restored *before* running the installer of the version you want. The installer will make sure the restored database is up-to-date as part of the installation.

If you want to use the built-in database, then start by running the installer. It will install the files and create a new ProGet database for you. From here, restore your database back-up (you can use the command above) and then run the Database Tool (available from inedo.com) for the version you are installing. That will ensure the database is up-to-date.
