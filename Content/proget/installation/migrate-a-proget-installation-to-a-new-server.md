---
title: "Migrate an Existing ProGet Installation to a New Server"
order: 7
---

When migrating your ProGet instance to a new Windows or Linux server, [feed replication](/docs/proget/feeds/feed-overview/proget-administration-migrating-a-proget-feed) is best for most cases, offering a fresh start, and being accessible through the ProGet UI. But there are times when a full server migration is needed, such as testing a version upgrade within a ProGet test environment.

When migrating a full ProGet installation with an [SQL](/docs/installation/sql-server) or [PostgreSQL](/docs/installation/postgresql) database, you should migrate to the same version first, then upgrade *after* you move your installation. Upgrading during your migration is possible, but can make the process much more complicated. Here are the instructions for migrating to the same version of ProGet.

## SQL Server Migration

:::(Info)
**ProGet** will support using [Microsoft SQL Server](https://www.microsoft.com/en-us/sql-server) as a database backend until 2027. Starting with ProGet 2025, [PostgreSQL](https://www.postgresql.org/) is the recommended database backend.
:::

### Prerequisites

You will first need to back up a few things from your original server (see the [Backing up & Restoring documentation](/docs/installation/backing-up-restoring) for more information):

- Your **database**, even if you are not moving your SQL server (`proget database export --file={export file}` or Adminsitration > Database Overview).
- Your **encryption key**, stored in your [shared config](/docs/installation/configuration-files) for LDAP and Cluster management, typically stored at `%PROGRAMDATA%\Inedo\SharedConfig\ProGet.config` on Windows or in your environment variables (`docker inspect proget`) on Linux.
- Your **package files** (Advanced Settings > `Storage.PackagesRootPath`).
- Your **extensions directory**, if you want your exact extensions and versions (Advanced Settings > `Extensions.ExtensionsPath`).

**Note:** If you are not using a shared config file, you will need to retrieve your encryption key from your `Web_appSettings.config` and the `App_appSettings.config` files found in your ProGet installation folder which should be installed to `C:\Program Files\ProGet` or `C:\ProGet`. If you are not using the shared config on the old server, the new server will install using the shared config, which you can copy your encryption key into.

Your *database connection string* is also stored in these files if you need to find it.

### Windows to Windows Migration

1. [Install ProGet](https://inedo.com/proget/download) on the new server. We recommend installing the same version as the one installed in your old server.
2. If you are changing to a new database server, restore your **database backup** to your new database server.
If your database name or SQL server name is different from what you installed with on the new server, you will need to update your connection string to point to that database in the [shared config](/docs/installation/configuration-files).
**Note:** You may need to re-add the user to the database that website and service use to access the database.
3. Copy your packages to your new server, using the exact same directory path your old server used. If your new server has a different path in `Storage.PackagesRootPath`, then update `Storage.PackagesRootPath` to point to the restored directory path.
4. If you backed up your extensions, copy them to the folder set in Advanced Settings > `Extensions.ExtensionsPath`.
5. On the new server, update the encryption key in your [shared config](/docs/installation/configuration-files) to its value when it was backed up.

### Windows to Linux Migration

1. [Install ProGet](https://inedo.com/proget/download) on the new server. We recommend installing the same version as the one installed in your old server.
Installing ProGet on Linux can be done via `docker run`, or with [Docker Compose](/docs/installation/linux/docker-compose-installation-guide).
2. If you are changing to a new database server, restore your **database backup** to your new database server.

You can also restore your database via `proget database restore` by copying your `.ahdbexp` file into the `/var/proget/backups` folder of your ProGet container.

If your database name or SQL server name is different from what you installed with on the new server, you will need to update your connection string to point to that database in your **environment variables**.

3. Copy your packages to your new server, using the exact same directory path your old server used. If your new server has a different path in `Storage.PackagesRootPath`, then update `Storage.PackagesRootPath` to point to the restored directory path.
4. If you backed up your extensions, copy them to the folder set in Advanced Settings > `Extensions.ExtensionsPath`.
5. On the new server, update the encryption key in your environment variables to its value when it was backed up.

### Linux to Windows Migration

1. [Install ProGet](https://inedo.com/proget/download) on the new server. We recommend installing the same version as the one installed in your old server.
2. If you are changing to a new database server, restore your **database backup** to your new database server.
If your database name or SQL server name is different from what you installed with on the new server, you will need to update your connection string to point to that database in the [shared config](/docs/installation/configuration-files).
**Note:** You may need to re-add the user to the database that website and service use to access the database.
3. Copy your packages to your new server, using the exact same directory path your old server used. If your new server has a different path in `Storage.PackagesRootPath`, then update `Storage.PackagesRootPath` to point to the restored directory path.
4. If you backed up your extensions, copy them to the folder set in Advanced Settings > `Extensions.ExtensionsPath`.
5. On the new server, update the encryption key in your `%PROGRAMDATA%\Inedo\SharedConfig\ProGet.config` to its value when it was backed up.

### Linux to Linux Migration

1. [Install ProGet](https://inedo.com/proget/download) on the new server. We recommend installing the same version as the one installed in your old server.
Installing ProGet on Linux can be done via `docker run`, or with [Docker Compose](/docs/installation/linux/docker-compose-installation-guide).
2. If you are changing to a new database server, restore your **database backup** to your new database server.

You can also restore your database via `proget database restore` by copying your `.ahdbexp` file into the `/var/proget/backups` folder of your ProGet container.

If your database name or SQL server name is different from what you installed with on the new server, you will need to update your connection string to point to that database in your **environment variables**.
3. Copy your packages to your new server, using the exact same directory path your old server used. If your new server has a different path in `Storage.PackagesRootPath`, then update `Storage.PackagesRootPath` to point to the restored directory path.
4. If you backed up your extensions, copy them to the folder set in Advanced Settings > `Extensions.ExtensionsPath`.
5. On the new server, update the encryption key in your environment variables to its value when it was backed up.

## PostgreSQL Sever Migration

### Prerequisites

You will first need to back up a few things from your original server (see the [Backing up & Restoring documentation](/docs/installation/postgresql) for more information):
- Your **database**, which can be exported as an `.ahdbexp` file in the ProGet UI (Administration > Database Overview), or via [Commands](/docs/installation/postgresql).
- Your **encryption key**, stored in your [shared config](/docs/installation/configuration-files) for LDAP and Cluster management, typically stored at `%PROGRAMDATA%\Inedo\SharedConfig\ProGet.config` on Windows or in your environment variables (`docker inspect proget`) on Linux.
- Your **package files** (Advanced Settings > `Storage.PackagesRootPath`).
- Your **extensions directory**, if you want your exact extensions and versions (Advanced Settings > `Extensions.ExtensionsPath`).

:::(Error)
**Warning:** The `.ahdbexp` file is intended for [migration between same ProGet versions only](/docs/installation/postgresql#database-import-export) and should not be relied upon for routine database backups.
:::

**Note:** If you are not using a shared config file, you will need to retrieve your encryption key from your `Web_appSettings.config` and the `App_appSettings.config` files found in your ProGet installation folder which should be installed to `C:\Program Files\ProGet` or `C:\ProGet`. If you are not using the shared config on the old server, the new server will install using the shared config, which you can copy your encryption key into.

Your *database connection string* is also stored in these files if you need to find it.

### Windows to Windows Migration

1. [Install ProGet](https://inedo.com/proget/download) on the new server, using the same version as the one installed on your current server.
2. Import your `.ahdbexp` file via the ProGet UI (Admin > Database Overview).
3. Update the encryption key in your [shared config](/docs/installation/configuration-files) to its value when it was backed up.
4. Your connection string will be read to connect to the PostgreSQL database of your new server.
5. Once restored, migrate your package files located in the `%ProgramData%\ProGet` directory to your new server (and any extensions). The default file paths for your ProGet instance can be found in Administration > Advanced Settings, and can be seen below:

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

### Windows to Linux Migration

1. [Install ProGet](https://inedo.com/proget/download) on the new server, using the same version as the one installed on your current server.
This can be done using `docker run`:

```bash
docker run -d --name=proget -p 8624:80 proget.inedo.com/productimages/inedo/proget:latest
```

**Note:** ProGet can also be installed on Linux via [Docker Compose](/docs/installation/linux/docker-compose-installation-guide).

2. Import your `.ahdbexp` file via the ProGet UI (Admin > Database Overview).

You can also restore your database via `proget database restore` by copying your `.ahdbexp` file into the `/var/proget/backups` folder of your ProGet container.

3. Update your encryption key in your Linux environment variables to its value when it was backed up.
4. Your conenction string will be read to connect to the PostgreSQL database of your new server.
5. Once restored, migrate your package files located in the `%ProgramData%\ProGet` directory to your new server (`/var/proget/packages`), along with any extensions. The default file paths for your Linux ProGet instance can be found in Administration > Advanced Settings, and can be seen below:

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

::: (WARNING)
**Note:** These file paths are visible on a fresh install of ProGet on Linux, but will be overwritten when restoring your migrated database. They can be updated in the ProGet UI.
:::

Running the following command, you can monitor the storage of your package directories to confirm your packages have successfully been migrated:

```bash
$ du -sh /var/proget/packages/<package-type>
```

:::(Info)
**Recommended:** After updating your file paths re-index your ProGEt feeds (Feeds > Manage Feed > Storage & Retention > re-index) to ensure feed stability.
:::

### Linux to Windows Migration

1. [Install ProGet](https://inedo.com/proget/download) on the new server, using the same version as the one installed on your current server.
2. Import your `.ahdbexp` file via the ProGet UI (Admin > Database Overview).
3. Update the encryption key in your [shared config](/docs/installation/configuration-files) to its value when it was backed up.
4. Your connection string will be read to connect to the PostgreSQL database of your new server.
5. Once restored, migrate your package files located in the `/var/proget/packages` directory to your new server (`%ProgramData%\ProGet`), along with any extensions. The default file paths for your Windows ProGet instance can be found in Administration > Advanced Settings, and can be seen below:

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

### Linux to Linux Migration

1. [Install ProGet](https://inedo.com/proget/download) on the new server, using the same version as the one installed on your current server.
This can be done using `docker run`:

```bash
docker run -d --name=proget -p 8624:80 proget.inedo.com/productimages/inedo/proget:latest
```

**Note:** ProGet can also be installed on Linux via [Docker Compose](/docs/installation/linux/docker-compose-installation-guide).

2. Import your `.ahdbexp` file via the ProGet UI (Admin > Database Overview).

You can also restore your database via `proget database restore` by copying your `.ahdbexp` file into the `/var/proget/backups` folder of your ProGet container.

3. Update your encryption key in your Linux environment variables to its value when it was backed up.
4. Your conenction string will be read to connect to the PostgreSQL database of your new server.
5. Once restored, migrate your package files located in the `/var/proget/packages` directory to the same directory on your new server, along with any extensions. The default file paths for your Linux ProGet instance can be found in Administration > Advanced Settings, and can be seen below:


| Default Linux File Paths |
| --- |
| `/usr/local/proget/extensions` |
| `/var/proget/extensions` |
| `/var/proget/backups ` |
| `/var/proget/packages/.apk` |
| `/var/proget/packages/.assets` |
| `/var/proget/packages/.cargo` |
| `/var/proget/packages/.composer` |
| `/var/proget/packages/.conan` |
| `/var/proget/packages/.cran` |
| `/var/proget/packages/.debian` |
| `/var/proget/packages/.docker/common` |
| `/var/proget/packages/.docker` |
| `/var/proget/packages/.helm` |
| `/usr/share/ProGet/LocalStorage` |
| `/var/proget/packages/.maven2` |
| `/var/proget/packages/.npm` |
| `/var/proget/packages/.nugetv2` |
| `/var/proget/packages` |
| `/var/proget/packages/.proget` |
| `/var/proget/packages/.pub` |
| `/var/proget/packages/.pypi` |
| `/var/proget/packages/.rpm` |
| `/var/proget/packages/.rubygems` |
| `/var/proget/packages/.terraform` |
| `/var/proget/packages/.vsix` |

Running the following command, you can monitor the storage of your package directories to confirm your packages have successfully been migrated:

```bash
$ du -sh /var/proget/packages/<package-type>
```

:::(Info)
**Recommended:** After updating your file paths re-index your ProGEt feeds (Feeds > Manage Feed > Storage & Retention > re-index) to ensure feed stability.
:::