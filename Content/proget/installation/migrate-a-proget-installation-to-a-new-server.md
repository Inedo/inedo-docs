---
title: "Migrate an Existing ProGet Installation to a New Server"
order: 7
---

In most cases, it's best to [migrate each feed](/docs/proget/feeds/feed-overview/proget-administration-migrating-a-proget-feed) separately. But there are times when a full server migration is needed. A common case for this is to test an upgrade by creating a ProGet test environment. In this case, a server migration is more applicable. This will also require a **ProGet trial key** or **ProGet Free key** to use in the non-production environment.

**Note:** When migrating a full ProGet installation, you should migrate to the same version first, then upgrade it _after_ you move your installation. Although it's possible to upgrade during the migration, it can make the process much more complicated. Here are the instructions for migrating to the same version of ProGet.

## How to Migrate ProGet to a New Server

:::(Info)
**ProGet** will support [Microsoft SQL Server](https://www.microsoft.com/en-us/sql-server) as a database backend until 2027. Starting with ProGet 2025, [PostgreSQL](https://www.postgresql.org/) is the recommended database backend.
:::

1. First, you will need to back up a few things from your original server (see the [Backing Up & Restoring documentation](/docs/installation/backing-up-restoring) for more information):
    1. Back up your database (you should do this even if you are not moving your SQL server).
    2. Back up your encryption key.
        - Your encryption key (required for LDAP and cluster management) is stored in your [shared config](/docs/installation/configuration-files). This is typically stored at `%PROGRAMDATA%\Inedo\SharedConfig\ProGet.config` on Windows or in your environment variables (`docker inspect proget`) on Linux.
        - If you are not using a shared config file, you will need to get the encryption key from your `Web_appSettings.config` and `App_appSettings.config` files. These are found in your ProGet installation folder, which should be installed to `C:\Program Files\ProGet` or `C:\ProGet`.
        - If you are not using the shared config on the old server, the new server will install using the shared config. You can copy your encryption key into the shared config on the new server.
        - Your database connection string is also stored in these files if you need to find it.
    3. Back up your package files.
        - You can find these by looking in the _Advanced Settings_ for the key `Storage.PackagesRootPath` (or `/var/proget/packages` if migrating from a Linux server).
    4. If you want your exact extensions and versions, then also back up your extensions directory.
        - You can find this in the _Advanced Settings_ and looking for the key `Extensions.ExtensionsPath`.
2. [Install ProGet](https://inedo.com/proget/download) on the new server. We recommend installing the same version as the one installed on your old server.
3. If you are changing to a new database server, restore the database to your new database server.
    - If your database name or SQL server name is different from what you installed with on the new server, you will need to update your connection string to point to that database in the [shared config](/docs/installation/configuration-files).
    - You may need to re-add the user to the database that your website and service use to access the database.
4. Copy your packages to the new server. You should copy these to the exact same directory path that the old server used.
    - If your new server has a different path in `Storage.PackagesRootPath`, then update `Storage.PackagesRootPath` to point to the restored directory path.
    - If this is different than the directory that ProGet was installed with, you will need to make sure that the user used by your web application and service has create/read/write/delete/modify access to that folder.
5. If you backed up your extensions, copy them to the folder set in `Extensions.ExtensionsPath` from _Advanced Settings_.
6. On the new server, update the EncryptionKey in your [shared config](/docs/installation/configuration-files) to the old value you backed up in step 1.
7. Restart your application pool in IIS and restart your ProGet service(s).

## Migrating to Windows or Linux

For PostgreSQL server migrations, your database can be exported as an `.ahdbexp` file in the ProGet UI (Administration > Database Overview), or via [Commands](/docs/installation/postgresql).

Simply import your `.ahdbexp` file via the ProGet UI (Admin > Database Overview) on your new server. Once you have updated your encryption key in your [shared config](/docs/installation/configuration-files) to its old value from when it was backed up, your connection string will be read to connect to the PostgreSQL database of your new server.

:::(Error)
**Warning:** The `.ahdbexp` file is intended for [migration between same ProGet versions only](/docs/installation/postgresql#database-import-export) and should not be relied upon for routine database backups.
:::

Once restored, you will need to migrate your package files (and any extensions) to the `%ProgramData%\ProGet` directory on your new server. The default file paths for a Windows ProGet instance (found in Administration > Advanced Settings) can be found below:

| Default Windows File Paths |
| --- |
| `Extensions.BuiltInExtensionsPath` |
| `Extensions.ExtensionsPath` |
| `Service.DatabaseBackupDirectory` |
| `Storage.ApkPackagesLibrary` |
| `Storage.AssetLibrary` |
| `Storage.CargoPackagesLibrary` |
| `Storage.ComposerPackagesLibrary` |
| `Storage.ConanPackagesLibrary` |
| `Storage.CranPackagesLibrary` |
| `Storage.DebianPackagesLibrary` |
| `Storage.DockerPackagesLibrary` |
| `Storage.DockerRepositoryLibrary` |
| `Storage.HelmPackagesLibrary` |
| `Storage.LocalStoragePath` |
| `Storage.MavenArtifactLibrary` |
| `Storage.NpmPackagesLibrary` |
| `Storage.NuGetPackagesLibrary` |
| `Storage.PackagesRootPath` |
| `Storage.ProGetPackagesLibrary` |
| `Storage.PubPackagesLibrary` |
| `Storage.PypiPackagesLibrary` |
| `Storage.RpmPackagesLibrary` |
| `Storage.RubyGemsLibrary` |
| `Storage.TerraformPackagesLibrary` |
| `Storage.VsixLibrary` |

For migrations to Linux, ProGet can be [installed](https://inedo.com/proget/download) via `docker run`:

```bash
docker run -d --name=proget -p 8624:80 proget.inedo.com/productimages/inedo/proget:latest
```

Or with [Docker Compose](/docs/installation/linux/docker-compose-installation-guide). You can then restore your database via `proget database restore` by copying your `.ahdbexp` file into the `/var/proget/backups` folder of your ProGet container, or by restoring via the ProGet UI (Admin > Database Overview). In the case of migrating to Linux, if your database name or SQL server name is different from what you installed with on the new server, you will need to update your connection string to point to that database in your _environment variables_.

To migrate your package files and extensions, whether migrating from a Windows to Linux server, or from a Linux to Linux server, the default file paths for your Linux ProGet instance (found in Administration > Advanced Settings) can be found below:

::: (WARNING)
**Note:** These file paths are visible on a fresh install of ProGet on Linux, but will be overwritten when restoring your migrated database. They can be updated manually in the ProGet UI.
:::

| Default Windows File Paths | Updated Linux File Paths |
| --- | --- |
| `Extensions.BuiltInExtensionsPath` | `/usr/local/proget/extensions` 
| `Extensions.ExtensionsPath` | `/var/proget/extensions`
| `Service.DatabaseBackupDirectory` | `/var/proget/backups `
| `Storage.ApkPackagesLibrary` | `/var/proget/packages/.apk`
| `Storage.AssetLibrary` | `/var/proget/packages/.assets`
| `Storage.CargoPackagesLibrary` | `/var/proget/packages/.cargo`
| `Storage.ComposerPackagesLibrary` | `/var/proget/packages/.composer`
| `Storage.ConanPackagesLibrary` | `/var/proget/packages/.conan`
| `Storage.CranPackagesLibrary` | `/var/proget/packages/.cran`
| `Storage.DebianPackagesLibrary` | `/var/proget/packages/.debian`
| `Storage.DockerPackagesLibrary` | `/var/proget/packages/.docker/common`
| `Storage.DockerRepositoryLibrary` | `/var/proget/packages/.docker`
| `Storage.HelmPackagesLibrary` | `/var/proget/packages/.helm`
| `Storage.LocalStoragePath` | `/usr/share/ProGet/LocalStorage`
| `Storage.MavenArtifactLibrary` | `/var/proget/packages/.maven2`
| `Storage.NpmPackagesLibrary` | `/var/proget/packages/.npm`
| `Storage.NuGetPackagesLibrary` | `/var/proget/packages/.nugetv2`
| `Storage.PackagesRootPath` | `/var/proget/packages`
| `Storage.ProGetPackagesLibrary` | `/var/proget/packages/.proget`
| `Storage.PubPackagesLibrary` | `/var/proget/packages/.pub`
| `Storage.PypiPackagesLibrary` | `/var/proget/packages/.pypi`
| `Storage.RpmPackagesLibrary` | `/var/proget/packages/.rpm`
| `Storage.RubyGemsLibrary` | `/var/proget/packages/.rubygems`
| `Storage.TerraformPackagesLibrary` | `/var/proget/packages/.terraform`
| `Storage.VsixLibrary` | `/var/proget/packages/.vsix`

After performing a migration to a new Linux server, the following command allows you to monitor the storage of your package directories to confirm your packages have successfully been migrated:

```bash
$ du -sh /var/proget/packages/<package-type>
```

:::(Info)
**Recommended:** After updating your file paths re-index your ProGet feeds (Feeds > Manage Feed > Storage & Retention > re-index) to ensure feed stability.
:::

### Linux to Windows Migration

When migrating a ProGet installation from Linux to Windows it again is possible to migrate your database by importing your `.ahdbexp` file via the ProGet UI (Admin > Database Overview). You will need to migrate your package files located in the `/var/proget/packages` directory to your new server (`%ProgramData%\ProGet`), along with any extensions, the default file paths being updated as follows:

| Default Linux File Paths | Updated Windows File Paths |
| --- | --- |
| `/usr/local/proget/extensions` | `Extensions.BuiltInExtensionsPath`
| `/var/proget/extensions` | `Extensions.ExtensionsPath`
| `/var/proget/backups ` | `Service.DatabaseBackupDirectory`
| `/var/proget/packages/.apk` | `Storage.ApkPackagesLibrary`
| `/var/proget/packages/.assets` | `Storage.AssetLibrary`
| `/var/proget/packages/.cargo` | `Storage.CargoPackagesLibrary`
| `/var/proget/packages/.composer` | `Storage.ComposerPackagesLibrary`
| `/var/proget/packages/.conan` | `Storage.ConanPackagesLibrary`
| `/var/proget/packages/.cran` | `Storage.CranPackagesLibrary`
| `/var/proget/packages/.debian` | `Storage.DebianPackagesLibrary`
| `/var/proget/packages/.docker/common` | `Storage.DockerPackagesLibrary`
| `/var/proget/packages/.docker` | `Storage.DockerRepositoryLibrary`
| `/var/proget/packages/.helm` | `Storage.HelmPackagesLibrary`
| `/usr/share/ProGet/LocalStorage` | `Storage.LocalStoragePath`
| `/var/proget/packages/.maven2` | `Storage.MavenArtifactLibrary`
| `/var/proget/packages/.npm` | `Storage.NpmPackagesLibrary`
| `/var/proget/packages/.nugetv2` | `Storage.NuGetPackagesLibrary`
| `/var/proget/packages` | `Storage.PackagesRootPath`
| `/var/proget/packages/.proget` | `Storage.ProGetPackagesLibrary`
| `/var/proget/packages/.pub` | `Storage.PubPackagesLibrary`
| `/var/proget/packages/.pypi` | `Storage.PypiPackagesLibrary`
| `/var/proget/packages/.rpm` | `Storage.RpmPackagesLibrary`
| `/var/proget/packages/.rubygems` | `Storage.RubyGemsLibrary`
| `/var/proget/packages/.terraform` | `Storage.TerraformPackagesLibrary`
| `/var/proget/packages/.vsix` | `Storage.VsixLibrary`