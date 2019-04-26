---
title: Performing an Entity Framework migration
Docs URL: /deployments/targets/databases/entity-framework-migration

Pre-requisites: Buildmaster, Microsoft SDK
---
Deploy and track database change scripts to the most popular database engines including SQL Server, My SQL, PostgreSQL, DB2, and Sybase

In addition to creating and managing build artifacts, BuildMaster is an automated deployment tool that can target virtually any platform used by any application in your organization.

A major part of deployment deals Deploy and track database change scripts. If you are using Entity Framework in your .NET application you can use BuildMaster to effectively run your migrations as part of your build plan.  

While Code First Migrations can be used to update a database from within visual studio, it can also be executed via the command line tool `migrate.exe`. This page will give a quick overview on how to use migrate.exe with BuildMaster to execute migrations against your target database.

#### Where is Migrate.exe?
When you install Entity Framework using NuGet migrate.exe will be inside the tools folder of the downloaded package. In `<project folder>\packages\EntityFramework.<version>\tools`

#### Copy Migrate.exe
Once you have found the migrate.exe file you'll need to copy it to the location of the assembly that contains your migrations.

After migrate.exe is in the correct folder you can execute an operation in BuildMaster to execute migrations against your target database. There are several migrate.exe [arguments](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/migrate-exe) that will allow you accomplish the migratation your application needs.  

Sample Usage:
```
migrate assembly  [configurationType]  [contextAssembly]  [/targetMigration]  [/startUpDirectory]  [/scriptFile]
        [/sourceMigration]  [/startUpConfigurationFile]  [/startUpDataDirectory]  [/connectionStringName]
        [/connectionString]  [/connectionProviderName]  [/force]  [/verbose]  [/?]
```

When running migrate.exe the only mandatory parameter is the assembly, which is the assembly that contains the migrations that you are trying to run. To do this you can simply add an `Exec` operation to your BuildMaster build plan.

Migrate to the latest migration:
```
InedoCore::Exec
(
    FileName: <pathToAssembly>\migrate.exe,
    Arguments: MyApplication.dll /startupConfigurationFile="..\\web.config"
);
```
Migrate to a specific migration:
```
InedoCore::Exec
(
    FileName: <pathToAssembly>\migrate.exe,
    Arguments: MyApplication.dll /startupConfigurationFile="..\\web.config" /targetMigration="AddNewField"
);
```

#### Notes:
- _migrate.exe doesn't support x64 assemblies._
- _This operation is designed to do is execute migrations. It cannot generate migrations or create a SQL script._

