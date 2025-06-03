---
title: "PostgreSQL & Inedo Products"
order: 13
---

<!--
:::(Internal)
Starting with ProGet 2025, we have embarked on our multi-year journey of [saying goodbye to SQL Server](https://blog.inedo.com/inedo/so-long-sql-server-thanks-for-all-the-fetch/). 
In its place, we will be switching to an embedded distribution of PostgreSQL with the option to use your own PostgreSQL Server. We also plan to support both databases for at least two major releases, to make the transition as painless as possible. There’s no rush to migrate immediately.
:::
-->

Inedo Products (starting with ProGet 2025) uses a custom build of PostgreSQL that's been security-hardened, stripped of unneeded features, and optimized for Inedo products. This database server can be hosted in two different ways.

* **Embedded**: For single-server installations, the database server instance is fully-managed by the Inedo product and will require no additional configuration or maintenance
* **InedoDB**: For clustered (multi-server) installations, the same custom build of PostgreSQL is packaged as InedoDB and installable on Windows or run as Docker container

Although most users will not need to interact with the database server, maintenance or troubleshooting may be required. This article will provide guidance on how to do that as well as describe advanced scenarios.

## Database Backup and Restore {#backup}

:::(Error)
The database is only a component of your Inedo product; see [Backing Up and Restoring](/docs/installation/backing-up-restoring) to learn how to Backup the entire installation.
:::


Backing up and restoring the database can be done using the `inedodb` CLI utility that's included in both distributions of InedoDB.

To backup, simply run `inedodb dump «product-name»` to create a time stamped archive in the current working directory, optionally with the `outfile` argument. For example, to backup ProGet:

```bash
$ inedodb dump proget --outfile=/var/backups/proget-20250812-1308.ahpak
```

To restore, you can use the `restore` command while specifying an `infile`. For example:

```bash
$ inedodb restore proget --infile=/var/backups/proget-20250812-1308.ahpak
```

Note that these commands are light wrappers for `pg_dump -Fc` and `pg_restore` that use information from the connection string stored in the [Installation Configuration File](/docs/installation/configuration-files) (Windows) or [Environment Variable](/docs/installation/linux/supported-environment-variables) (Linux).



## InedoDB Installation & Upgrade {#inedodb}

InedoDB is a custom build of PostgreSQL that's been security-hardened, stripped of unneeded features, and optimized for Inedo products. It's required for clustered (multi-server) installations.

:::(Warning) (🚧 InedoDB is Coming Soon 🚧)
We are planning on releasing the first version of InedoDB in 2025/Q4. This documentation is preliminary.

If you wish to use a clustered installation with PostgreSQL without an [external PostgreSQL servers](#external-postgres), you'll need to wait until then.
:::

### Releases & Downloads {#inedodb-releases}

The following versions of the InedoDB are available:

| Version | Released | Downloads | Notes
| -- | -- | --
| 17.4.0 | TBD XX, 2025 | [installer exe](#) | Initial Release

For new installations, you should use the latest version unless you plan to restore a database backup. In that case, use the same version of InedoDB that was used to create that backup.

<!--
#inedodb-releases}
Note that the first two digits of InedoDB's version correspond to the [PostgreSQL version number](https://www.postgresql.org/support/versioning/) used.
-->


### Installation on Linux (Docker)

InedoDB is available from the `proget.inedo.com/productimages/inedo/inedodb` repository.

:::(Error)
**Do not use the `latest` tag**. Instead, use the most recent version under [Releases & Downloads](#inedodb-releases}).

Upgrading InedoDB requires special consideration, and suddenly changing the version of your container may cause the database to become inoperable.
:::

To start an InedoDB container, use this command:

```bash
docker run --name inedodb \
  -p 5432:5432 -e POSTGRES_PASSWORD=«YourStrong!Passw0rd» \
  --restart=unless-stopped \
  -d proget.inedo.com/productimages/inedo/inedodb:17.4.0
```

Once you have an SQL Server instance running, you'll need to initialize a product database using `inedodb initdb «product-name»` command.

### Example: ProGet SQL Server Database
To initialize a database for ProGet on the InedoDB instance running in the `inedodb` container:

```bash
docker exec -it inedodb /opt/tools/inedodb initdb ProGet
```

### Installation on Windows

InedoDB is available from the links under [Releases & Downloads](#inedodb-releases}).

Simply download and run the installer. You'll be prompted for a port number to use and a password.

### Upgrading InedoDB

We don't recommend upgrading unless you were prompted to by a newer version of an Inedo product or directed to by a support engineer.

Before upgrading to a new major version of InedoDB (e.g., from 17.4.1 to 18.0.0), you will need to [backup the databases](#backup). After installing the new version, you will need to restore them.

When upgrading to a new, non-major version of InedoDB (e.g., from 17.4.1 to 17.5.0), backing up and restoring is not necessary.

## External PostgreSQL Servers { #external-postgres }

:::(Warning)
**Experts Only.** Using an External PostgreSQL Server is for experts only.
:::

It's also possible to connect to an external PostgreSQL, but we don't recommend it unless you have sufficient expertise in managing PostgreSQL servers and databases.

If you wish to use an external PostgreSQL Server, note that the only supported versions are indicated by the first two digits of the [InedoDB releases numbers](#inedodb-releases) (e.g. InedoDB 17.4.0 means that PostgreSQL 17.4 is supported). Other versions of PostgreSQL may work, but we have not tested them nor will we support any PostgreSQL-related issues.

To use an external PostgreSQL Server, first create the a database for ProGet using the `initdb` command with the desired data directory (`-D`), desired credentials (e.g. `-U` and `--pwfile`), and the following arguments:
```
--encoding=UTF8 --locale-provider=icu --icu-locale=en-US.utf8
```

Note that the user specified in `-U` will be used in the connections string and must have Database Owner Permission. 

Then, set the connection string in the [Installation Configuration File](/docs/installation/configuration-files) (Windows) or [Environment Variable](/docs/installation/linux/supported-environment-variables) (Linux) based on this template

```
Server=myserver.corp;Port=5432;Database=myDataBase;User Id=myUsername;Password=myPassword;
```

Without explicit guidance from our support team, we do not support additional arguments/configuration when creating the database or in the connection string.

## Querying & Modifying The Database { #querying }

:::(Error)
**No Schema Modifications**; no aspects of the database schema (including indexes or procedures) may be modified without explicit guidance from our support team
:::

We support querying databases but we do not offer support for writing to them. Tables, views, and stored procedures are closely linked with foreign keys and indexes. Should you add or change records this may lead to instability within the applications.

If you wish to gain a better understanding of the structure or seek other support, we welcome you to contact our engineers through our [Support Page](https://inedo.com/support) for any assistance or inquiries you may have.


## Database Import & Export

You can export the contents of a database from the web application, whether using SQL Server or PostgreSQL. This export file is **not** intended to be a backup, and only has a few very specific uses:

 * Migrating from SQL Server to PostgreSQL without using the in-place migration
 * Migrating from one server to another
 * Sharing a database with Inedo for support purposes

The **only** use for a database exported in this manner is to be immediately imported on another server for migration purposes, or to submit to Inedo for a support ticket. The ProGet version of the importing server must match the version which exported this file exactly.


## Migrating from SQL Server { #migrating-from-sqlserver }

To migrate from SQL Server,  navigate to Administration > Database Overview and follow the prompts.