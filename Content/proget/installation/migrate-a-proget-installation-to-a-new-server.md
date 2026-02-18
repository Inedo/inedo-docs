---
title: "Migrate an Existing ProGet 2025+ Installation to a New Server"
order: 7
---

When migrating your ProGet instance to a new Windows or Linux server, [feed replication](/docs/proget/feeds/feed-overview/proget-administration-migrating-a-proget-feed) is best for most cases, offering a fresh start, and being accessible through the ProGet UI. But there are times when a full server migration is needed, such as testing a version upgrade within a ProGet test environment.

When migrating a full ProGet installation with a PostgreSQL database, you should migrate to the same version first, then upgrade after you move your installation. Here are the instructions for migrating to the same version of ProGet.

:::(Info)
This article is for migrating ProGet 2025+ using a PostgreSQL database. [See here for migrating older versions of ProGet.](/docs/proget/installation/migrate-a-proget-installation-to-a-new-server)
:::

## Migrating From Windows to Windows

You will need to [back up a few things](/docs/installation/backing-up-restoring) from your original server, including your [Database](/docs/installation/postgresql#inedodb-commands), which will be downloaded as an `.ahbak` file, [Configuration File](/docs/installation/backing-up-restoring#installation-configuration-file-backup), and [Package Files](/docs/installation/backing-up-restoring#package-files-backup). You can use the built-in backup job (Admin > Database Overview) or the [PostgreSQL Database Backup and Restore command](/docs/installation/postgresql#backup) to do this.

![ProGet Database](/resources/docs/installation-migration-databasebackup.png){height="" width="50%"}

Install ProGet on the new server, using the same version as the one installed on your current server. Once installed, it's possible to restore your database backup using the following command:

```bash
proget database restore --file=C:\Backups\ProGetBackup.ahbak
```

If you have InedoDB installed, the following restore command can be used:

```bash
inedodb restore proget --infile=C:\Backups\ProGetBackup.ahbak
```

Your connection string will be read to connect to the PostgreSQL database of your new server.

Once restored, migrate your package files located in the `%ProgramData%\ProGet` directory to your new server. The file paths for your ProGet instance can be found in Admin > Advanced Settings.

Alternatively, an `.ahdbexp` file can be exported via the ProGet UI (Admin > Database Overview), then imported via the same menu in your new ProGet instance.

![ProGet Database](/resources/docs/installation-migration-databaseexport.png){height="" width="50%"}

::: (WARNING) (Note)
The `.ahdbexp` file is intended for [migration between same ProGet versions only](/docs/installation/postgresql#database-import-export) and should not be relied upon for routine database backups.
:::

## Migrating From Windows to Linux

[Backup the essential things](/docs/installation/backing-up-restoring) from your original server, including your [Database](/docs/installation/backing-up-restoring#database-backup), [Configuration File](/docs/installation/backing-up-restoring#installation-configuration-file-backup), and Package Files. You can use the built-in backup job (Admin > Databse Overview) or the [PostgreSQL Database Backup and Restore command](/docs/installation/postgresql#backup) to do this.

Install ProGet on your Linux server. ProGet 2025+ can be installed on Linux using the [official Docker image](/docs/installation/linux/docker-guide), creating three directories (proget-packages, proget-database, proget-backups), for the [specified volumes](/docs/installation/linux/docker-guide#embedded-database-volumes-proget-2025-only). You can check your instance is running with the follwing command:

```bash
$ docker ps
```

Or

```bash
$ docker logs proget
```

Send your database backup file to your Linux server via SCP:

```bash
scp C:\User\Downloads\ProGetBackup.ahbak<server-name>@<server_address>:/tmp/
```

Move your .ahbak file into the backups path of your Linux based ProGet instance:

```bash
$ docker cp /tmp/ProGetBackup.ahbak proget:/var/proget/backups
```

A successful file move can be verified by running the following:

```bash
$ Proget ls -lh /var/proget/backups
```

Your database can be manually restored from your database backup file:

```bash
$ docker exec -it proget \
/usr/local/proget/service/proget database restore \
--file=/var/proget/backups/ProGetBackup.ahbak
```

With your database migrated, you can begin migrating your package files to your Linux server. Your ProGet instance file paths, located at Admin > Advanced Settings will need to be changed from Windows paths to the following:



| Default Windows Paths | Updated Linux Paths |
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

::: (WARNING) (Note)
These file paths are visible on a fresh install of ProGet on Lnux, but will be overwritten when restoring your migrated database, necessitating their replacement.
:::

Once your file paths are changed, select Save Changes.

Running the following command, you can monitor the storage of your package directories to confirm your packages have successfully been migrated:

```bash
$ du -sh /var/proget/packages/<package-type>
```

:::(Info)
Recommended: After updating your file paths re-index your ProGEt feeds (Feeds > Manage Feed > Storage & Retention > re-index) to ensure feed stability.
:::