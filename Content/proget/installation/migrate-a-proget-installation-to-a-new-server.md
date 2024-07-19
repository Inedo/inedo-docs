---
title: "Migrate an Existing ProGet Installation to a New Server"
order: 7
---

Most cases, it is best to [migrate each feed](/docs/proget/feeds/feed-overview/proget-administration-migrating-a-proget-feed) separately.  But there are times when a full server migration is needed.  A common case for this is to test an upgrade by creating a ProGet test environment.  In this case, a server move is more applicable.  This will also require a ProGet trial key or ProGet Free key to use in the non-production environment.

When migrating a full ProGet installation, you should migrate to the same version first, then upgrade it _after_ you move your installation.  Although you can upgrade during the migration, it can make this process much more complicated.  Here are the instructions for migrating to the same version of ProGet.

## How to Migrate ProGet to a New Server

1. You will first need to back up a few things from your original server (see the [Backing up & Restoring documentation](/docs/installation/backing-up-restoring) for more information):
    1. Back up your database (you should do this even if you are not moving your SQL server).
    2. Back up your encryption key.
        - Your encryption key is stored in your [shared config](/docs/installation/configuration-files). This is typically stored at `%PROGRAMDATA%\Inedo\SharedConfig\ProGet.config`.
        - If you are not using a shared config file, you will need to get the encryption key from your `Web_appSettings.config` and the `App_appSettings.config` files found in your ProGet installation folder which should be installed to `C:\Program Files\ProGet` or `C:\ProGet`.
        - If you are not using the shared config on the old server, the new server will install using the shared config. You can copy your encryption key into the shared config on the new server.
        - Your database connection string is also stored in these files if you need to find it.
    3. Back up your package files.
        - You can find these by looking in the _Advanced Settings_ for the key `Storage.PackagesRootPath`.
    4. If you want your exact extensions and versions, then also back up your extensions directory.
        - You can find this in the _Advanced Settings_ and looking for the key `Extensions.ExtensionsPath`.
2. Install ProGet on the new server. We recommend installing the same version as the one installed on your old server.
3. If you are changing to a new database server, restore the database to your new database server.
    - If your database name or SQL server name is different from what you installed with on the new server, you will need to update your connection string to point to that database in the  [shared config](/docs/installation/configuration-files).
    - You may need to re-add the user to the database that your website and service use to access the database.
4. Copy your packages to the new server. You should copy these to the exact same directory path that the old server used.
    - If your new server has a different path in `Storage.PackagesRootPath`, then update `Storage.PackagesRootPath` to point to the restored directory path.
    - If this is different than the directory that ProGet was installed with, you will need to make sure that the user used by your web application and service has create/read/write/delete/modify access to that folder.
5. If you backed up your extensions, copy them to the folder set in `Extensions.ExtensionsPath` from _Advanced Settings_.
6. On the new server, update the EncryptionKey in your [shared config](/docs/installation/configuration-files) to the old value you backed up in step 1.
7. Restart your application pool in IIS and restart your ProGet service(s).