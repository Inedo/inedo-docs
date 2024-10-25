---
title: "Migrating BuildMaster to a New Server"
order: 4
---

It may be necessary to move all or part of an existing [BuildMaster instance](https://inedo.com/buildmaster) to a different server. 

## Migrating an Entire Instance

We strongly recommend using the same version on both servers. While you can upgrade at the same time, it can make the move process much more complicated.

### Back-up from Old Server
First, you need to back up some data from your original server:
1. Back up your database (you should do this even if you are not moving your SQL server).
2. Back up your encryption key.
    *   Your encryption key is stored in [shared config](/docs/installation/configuration-files). This is typically stored at `%PROGRAMDATA%\Inedo\SharedConfig\BuildMaster.config`.
    *   If you are not using a shared config file, you will need to get the encryption key from your `Web_appSettings.config` and the `App_appSettings.config` files found in your BuildMaster installation folder which should be installed to `C:\Program Files\BuildMaster` or `C:\BuildMaster`.
    *   If you are not using the shared config on the old server, the new server will install using the shared config. You can copy your encryption key into the shared config on the new server.
    *   Your database connection string is also stored in these files if you need to find it.
3.  Back up your artifact files.
    *   You can find these by looking in the _Advanced Settings_ for the key `Artifacts.BasePath`.
4.  If you want your exact extensions and versions, then back up your extensions directory.
    *   You can find this in the _Advanced Settings_ and looking for the key `Extensions.ExtensionsPath`.

### Prepare New Server
Install BuildMaster on the new server. We recommend installing the **same** version that is installed on your old server.

### Perform Migration
1.  Stop all of your BuildMaster services and Agents on all servers.
2.  If you are changing to a new database server, restore the database to your new database server.
    *   If your database name or SQL server name is different from what you installed with on the new server, you will need to update your connection string to point to that database in the [shared config](/docs/installation/configuration-files).
    *   You may need to re-add the user to the database that your website and service use to access the database.
3.  Copy your artifacts to the new server. You should copy these to the **exact same directory path** that the old server used.
    *   If your new server has a different path in `Artifacts.BasePath`, update `Artifacts.BasePath` to point to the restored directory path.
    *   If this is different than the directory that BuildMaster was installed with, you will need to make sure that the user used by your web application and service have read/write/delete access to that folder.
    *   If you have configured BuildMaster to use a Raft Repository, refer to our [Raft Repository](/docs/buildmaster/administration/rafts) documentation for properly moving and configuring it.
4.  If you backed up your extensions, copy them to the folder set in `Extensions.ExtensionsPath` from _Advanced Settings_.
5.  On the new server, update the `EncryptionKey` in your [shared config](/docs/installation/configuration-files) to the old value you backed up in step 1.
6.  Restart your application pool in IIS, and start your BuildMaster service(s) and/or Agent(s).

## Migrating Application-by-Application

If you aren't happy with the way you're using BuildMaster, or need to use legacy features from BuildMaster 6.1, you can migrate application-by-application. This does *not* require the same version of BuildMaster to be installed on both servers.

:::(Info) (BuildMaster 6.1 Note)
If you are upgrading from BuildMaster 6.1, be sure to familiarize yourself with the new version first: Read the Getting Started guide, go through the tutorial, and look at some of the sample application templates. You may discover a new and better way to use BuildMaster in your organization than the one you have been using. In this case, it may be better for you to just "start over" or manually copy parts of existing plans as needed.
:::

### Individual Application Import

Export a single application at a time from BuildMaster to a universal package, and import that package into your new BuildMaster instance. This will give you a good opportunity to discover any issues and gradually migrate your applications to the new instance.

In general, this approach works best if you have a large number of applications and some of them rely on legacy features that have been removed. This way, you can take over the applications as the legacy dependencies are removed to take advantage of the new features and capabilities, while keeping the original instance to mitigate risk.

### Multiple Application Import

When you're satisfied with the import of individual applications, you can export all remaining applications from your BuildMaster and import them into the new BuildMaster instance.

## Additional Migration Notes

*   Consider **exporting your infrastructure** from the original instance and importing into the new one before doing anything else. This will prevent you from having to recreate servers, roles, and environments in the new instance. Of course, if you want to start fresh, you may want to skip this.
*   Upgrade your Inedo Agents to the latest version, and set the **`Agents.InstanceId`** advanced configuration setting to a unique value in your new BuildMaster instance. This will allow your second instance to reuse the same agents without any interference.
*   Consider **enabling [CEIP](/docs/installation/logging/diagnostic-center#ceip)**. This data is very valuable in case you experience any bugs or performance issues.
*   Don't forget about **permissions**! Make sure you copy over any required permissions, or start from scratch if your original list has gotten too complicated.