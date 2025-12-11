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

Inedo Products (starting with ProGet 2025) use a custom distribution of PostgreSQL that's been security-hardened, stripped of unneeded features, and optimized for Inedo products. We are currently planning to include this database backend in BuildMaster 2026 and Otter 2026.

Our custom PostgreSQL server can be hosted in two different ways.

* **Embedded**: For single-server installations, the database server instance is fully-managed by the Inedo product and will require no additional configuration or maintenance
* **InedoDB**: For clustered (multi-server) installations, the same custom build of PostgreSQL is packaged as InedoDB and installable on Windows or run as Docker container

Although most users will not need to interact with the database server, maintenance or troubleshooting may be required. This article will provide guidance on how to do that as well as describe advanced scenarios.

## Database Backup and Restore {#backup}

:::(Error)
The database is only a component of your Inedo product; see [Backing Up and Restoring](/docs/installation/backing-up-restoring) to learn how to Backup the entire installation.
:::


Backing up and restoring the database can be done with a simple `dump` and `restore` command.

### Embedded Database Commands

For Embedded databases, these commands are run using the product's main executable (e.g. `proget` or `proget.exe`), in the program directory.

To backup, simply run `«product-cli» database dump ` to create a time stamped archive in the current working directory, optionally with the `outfile` argument. For example, to backup ProGet:

```bash
$ proget database dump --file=/var/backups/proget-20250812-1308.ahbak
```

To restore, you can use the `restore` command while specifying an `file`. For example:

```bash
$ proget database restore --file=/var/backups/proget-20250812-1308.ahbak
```

### InedoDB Commands

InedoDB includes a `inedodb` CLI utility to run these commands.

To backup, simply run `inedodb dump «product-name»` to create a time stamped archive in the current working directory, optionally with the `outfile` argument. For example, to backup ProGet:

```bash
$ inedodb dump proget --outfile=/var/backups/proget-20250812-1308.ahbak
```

To restore, you can use the `restore` command while specifying an `infile`. For example:

```bash
$ inedodb restore proget --infile=/var/backups/proget-20250812-1308.ahbak
```

### PostgreSQL Commands

Note that these `dump` and `restore` commands are light wrappers for `pg_dump -Fc` and `pg_restore` that use information from the connection string stored in the [Installation Configuration File](/docs/installation/configuration-files) (Windows) or [Environment Variable](/docs/installation/linux/docker-guide#supported-environment-variables) (Linux).

## InedoDB Installation & Upgrade {#inedodb}

InedoDB is a custom PostgreSQL distribution that's been security-hardened, stripped of unneeded features, and optimized for Inedo products. It's required for clustered (multi-server) installations.


### Releases & Downloads {#inedodb-releases}

The following versions of the InedoDB are available:

| Version | Released | Downloads | Notes
| -- | -- | --
| 17.6.0 | Aug 22, 2025 | [installer exe](https://cdn.inedo.com/downloads/inedodb/InedoDBInstaller17.6.0.exe) | Initial Release

For new installations, you should use the latest version unless you plan to restore a database backup. In that case, use the same major version of InedoDB that was used to create that backup.

InedoDB follows PostgreSQL's versioning scheme for its Major.Minor release numbers. You can upgrade or downgrade among minor releases, and InedoDB releases closely follow PostgreSQL's minor release schedule.

Support for upgrading from one major release to another will be added in a future release of InedoDB. Currently, it is always based on PostgreSQL 17.


### Installation on Linux (Docker)

InedoDB is available from the `proget.inedo.com/productimages/inedo/inedodb` repository.

:::(Error)
**Do not use the `latest` tag**. Instead, use the most recent version under [Releases & Downloads](#inedodb-releases}).

Upgrading InedoDB requires special consideration, and suddenly changing the version of your container may cause the database to become inoperable.
:::

To start an InedoDB container, use this command:

```bash
docker run --name inedodb \
  --restart=unless-stopped \
  -d proget.inedo.com/productimages/inedo/inedodb:17.6
```

Once you have an instance running, you'll need to initialize a product database using the `docker exec inedodb inedodb create «product-name»` command.

### Example: ProGet InedoDB Database
To initialize a database for ProGet on the InedoDB instance running in the `inedodb` container:

```bash
docker exec inedodb inedodb create proget
```

### Installation on Windows

InedoDB is available from the links under [Releases & Downloads](#inedodb-releases}).

Simply download and run the installer. You'll be prompted for a data directory to store the database files.


### Upgrading InedoDB

Currently, InedoDB only uses PostgreSQL 17. You may upgrade to any other 17.X.Y version without backing up your database, as the data files are not modified by this upgrade process.

In the future, we will provide an InedoDB that uses a newer major PostgreSQL version; check back here for guidance on when to install it.


## External PostgreSQL Servers { #external-postgres }

:::(Warning)
**Experts Only.** Using an External PostgreSQL Server is for experts only.
:::

It's also possible to connect to an external PostgreSQL, but we don't recommend it unless you have sufficient expertise in managing PostgreSQL servers and databases.

If you wish to use an external PostgreSQL Server, note that the only supported versions are indicated by the first digit of the [InedoDB releases numbers](#inedodb-releases) (e.g. InedoDB 17.4.0 means that PostgreSQL 17 is supported). Other versions of PostgreSQL may work, but we have not tested them, nor will we support any PostgreSQL-related issues when an insupported version is used.

To use an external PostgreSQL Server, first create the a database for ProGet using the `initdb` command with the desired data directory (`-D`), desired credentials (e.g. `-U` and `--pwfile`), and the following arguments:
```
--encoding=UTF8 --locale-provider=icu --icu-locale=en-US.utf8
```

Note that the user specified in `-U` will be used in the connections string and must have Database Owner Permission. 

Then, set the connection string in the [Installation Configuration File](/docs/installation/configuration-files) (Windows) or [Environment Variable](/docs/installation/linux/docker-guide#supported-environment-variables) (Linux) based on this template

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

### Querying The Database Using the CLI

You can query the PostgreSQL database using the `query` operation in the product's main executable (e.g. `proget` or `proget.exe`) in the program directory.  The `query` operation takes a `--file` argument that points to a `.sql` file containing the SQL query and will print the results in CSV format to the standard output.  It uses the product's configured PostgreSQL connection string to execute **read-only** queries against the PostgreSQL database.  Example: `proget query --file=myquery.sql`

:::(Info)(Note:)
The `query` operation is typically only used for troubleshooting or support purposes. It is not intended to be a general-purpose SQL query tool.
:::

#### Example: Query for a list of feeds to a file on Windows

1. Create a file named `list-feeds.sql` with the following contents:

    ```sql
    SELECT "Feed_Id","Feed_Name", "Feed_Description", "Active_Indicator", "FeedType_Name"
    FROM Feeds
    ORDER BY Name;
    ```
2. Run the following command from a command prompt in the ProGet program directory:

    ```bash
    proget query --file=list-feeds.sql > list-feeds.csv
    ```

#### Example: Query for a list of feeds to a file on Docker

1. Connect to the ProGet container interactively

    ```bash
    docker exec -it «NAME_OF_PROGET_CONTAINER» bash
    ```
1. Create a file named `/home/list-feeds.sql` with the following contents:

    ```sql
    SELECT "Feed_Id","Feed_Name", "Feed_Description", "Active_Indicator", "FeedType_Name"
    FROM Feeds
    ORDER BY Name;
    ```
3. Run the following command from a command prompt in the ProGet program directory:

    ```bash
    ./usr/local/proget/service/proget query --file="/home/list-feeds.sql" > "/home/list-feeds.csv"
    ```

## Database Import & Export

You can export the contents of a database from the web application, whether using SQL Server or PostgreSQL. This export file is **not** intended to be a backup, and only has a few very specific uses:

 * Migrating from SQL Server to PostgreSQL without using the in-place migration
 * Migrating from one server to another
 * Sharing a database with Inedo for support purposes

The **only** use for a database exported in this manner is to be immediately imported on another server for migration purposes, or to submit to Inedo for a support ticket. The ProGet version of the importing server must match the version which exported this file exactly.


## Migrating from SQL Server { #migrating-from-sqlserver }

To migrate from SQL Server, navigate to Administration > Database Overview and follow the prompts and guidance to have ProGet create a PostgreSQL database and then copy data from SQL Server.

ProGet's API will be disabled during the migration, which may take anywhere from a few minutes to an hour or more. If there are any issues, you can revert to SQL Server.

### InedoDB / Clustered Installations

For clustered installations, simply configure InedoDB as if you were setting up a new installation, including creating a blank product database.

Then, you can migrate by:
1. Disabling access to your cluster at the load balancer
2. Exporting the database to a file under Admin > Database Overview or by running `proget database export` at the commandline
3. Connecting the ProGet cluster to the new InedoDB instance by editing the connection string
4. Importing the database file under Admin > Database Overview or by running `proget database import` at the commandline
5. Reenable access to the cluster


If there are issues using InedoDB, you can simply change the cluster to point to SQL Server again.
