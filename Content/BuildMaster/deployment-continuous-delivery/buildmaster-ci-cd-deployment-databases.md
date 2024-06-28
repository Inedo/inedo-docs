---
title: "Databases"
order: 6
---

Database changes are not only more complicated to deploy than files, but they tend to be riskier. BuildMaster has several features to help you manage and automate database changes for your releases and supports all four of the major relational databases:

- SQL Server
- MySQL
- Oracle
- Postgres

Database support is implemented through an [extensible feature](/docs/buildmaster/reference/extensions), and additional databases may be supported by creating custom extensions.    

## Database Connections

Database connections are essentially named database connection strings for a specific type of database (SQL Server, MySQL, Oracle, Postgres, etc.) that are associated with an [environment](/docs/buildmaster/administration-agents-and-infrastructure/environments).

The [database operations](/docs/buildmaster/reference/operations) rely on these connection names, whereas the environment is used for security purposes:     

- Database connections can be restricted to deployment plans targeting a particular environment. This means users who have permission to create and execute deployment plans in testing environments may not use database connections meant for production environments.
- Users may be prevented from viewing these database connection strings by defining an [access control](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-security).

Database connections are also associated with a server. This server is only for manual database change script deployments. When selected, the deployment will run from the selected server, as if a "for server" statement was used in OtterScript.

## Database Operations

BuildMaster has several built-in database operations that you can use in your [deployment plans](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts). They all require a database connection to use.

[Execute Database Statement](/docs/buildmaster/reference/operations/databases/execute-database-statement) will run the specified SQL Statement against the specified connection.  

[Execute Database Scripts on Disk](/docs/buildmaster/reference/operations/databases/execute-database-scripts-on-disk) will read the contents of the specified files and execute them as statements against the specified connection.   

[Backup Database](/docs/buildmaster/reference/operations/databases/backup-database) and [Restore Database](/docs/buildmaster/reference/operations/databases/restore-database) will perform a backup or restore of a specific database to/from a file on disk. This runs a simple SQL command specific to the database connection type (SQL Server, MySQL, Oracle, etc.) and does not offer many options. For a full-feature backup or restore, use the Execute Database Statement operation.   

[Execute Database Change Scripts](/docs/buildmaster/reference/operations/databases/execute-database-change-scripts) and Build Database Updater Executable are described in more detail below.  

## Object Deployment

In order to deploy database objects (stored procedures, views, etc.) with BuildMaster, you should first put these objects in source control. This is done by generating SQL Script files that drop and create the object, as prescribed in Database Changes Done Right.

Next, you can use the source control operations to get files on disk, then use the [Execute Database Scripts on Disk](/docs/buildmaster/reference/operations/databases/execute-database-scripts-on-disk) to execute those scripts.

## Change Scripts (DDL/DML)

Some scripts that you run against a database can make irrevocable changes to your database. For example, a data-definition (DDL) script can drop a column from a table and a data-manipulation (DML) script may delete hundreds of rows.    

Because these types of scripts can be run once (and only once), and cannot be undone without running another script or restoring a database backup, they can be particularly challenging to incorporate into your release process. BuildMaster can help manage this process with database change script assets.    

You can add a database change script asset by going to the application’s Assets > SQL Scripts, and adding a script.  These scripts may then be manually executed from the user interface or deployed within a deployment plan using the [Execute Database Change Scripts](/docs/buildmaster/reference/operations/databases/execute-database-change-scripts) operation.


### Release Number Ordering

When adding a change script, you will select a release to associate the script with. This release is used by the Execute Database Change Scripts operation to determine whether or not the script should be executed during a deployment.    

For example, if you select 1.2.0 as the release, and deploy a build for release 1.1.0, then the script will not be executed because it’s for a later release. However, if you deploy a build for 2.0.0, then all database change scripts associated with earlier releases (1.2.0, 1.1.0, etc.) will be executed.

This logic allows you to restore an older database and bring it up to the target version.  

### Metadata Table

Database change script executions are tracked using a lightweight table that is automatically added to the target database when change scripts are first run against it. If you set the Initialize Database property to false on the Execute Database Change Scripts operation, and this table does not already exist, an error will occur. This can guard against mistaken connection strings.   

### Metadata Execution

To manually deploy a database change script, you can click the deploy button on the change script listing page and select the target connection. This will cause an execution to be dispatched that runs the change script against the specified connection.    

## Data Deployment Tool

In addition to running change scripts from within BuildMaster, sometimes it is necessary to deploy change scripts to a system not associated with the installation. BuildMaster is able to generate a simple command-line executable that has no dependencies (other than .NET 4.5); it may be used to execute change scripts against any arbitrary database, just as if this were done from within BuildMaster.     

You can use the Build Database Updater Executable operation within a deployment plan to generate this executable, or do it from the database change scripts page in the user interface.    

### Using the Change Script Package

The executable generated for running change scripts is not designed to be user-friendly. It is a simple command-line utility meant to be used by developers or wrapped by some type of installer. The tool may be run interactively or arguments may be specified using the command-line.