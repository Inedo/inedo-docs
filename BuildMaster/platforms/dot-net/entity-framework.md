---
title: Entity Framework
subtitle: Performing an Entity Framework Migration
sequence: 600 
keywords: buildmaster, entity-framework, sql-server, dot-net
show-headings-in-nav: true
---

[Entity Framework](https://docs.microsoft.com/en-us/ef/) (EF) is Microsoft's ORM designed for .NET applications, allowing devlopers to effectively build database schemas in any platform (e.g. SQL Server, MySQL, PostgreSQL) in C# or Visual Basic, as opposed to creating them by writing SQL directly. This process, known as a *code-first*, can increase the development cycle in smaller organizations, but also introduce deployment challenges in larger organizations where specialized teams perform database deployments. For those organizations, [scaffolding](https://docs.microsoft.com/en-us/ef/core/managing-schemas/scaffolding) (i.e. generating the .NET objects *after* the database schema is created) may be the best solution.

BuildMaster is capable of deploying database changes resulting from changes to the ORM objects. These changes are known as *migrations*. Migrations can be performed directly from Visual Studio, the .NET Core CLI tool, or the standalone tool `ef6.exe`. 

As an alternative to migrations, change scripts could be [generated](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli#generate-sql-scripts) and executed using BuildMaster's built-in [change script management](/docs/buildmaster/deployments/targets/databases) process.

## Get the `ef6.exe` CLI Tool {#get data-title="Get ef6.exe"}

The `ef6.exe` command line tool is found in the `tools/` subfolder of the [EntityFramework NuGet package](https://www.nuget.org/packages/EntityFramework). Because it is a standalone tool and NuGet packages are ZIP files, the executable can be extracted directly from the package in Windows by downloading the package, changing the extension from `.nupkg` to `.zip`, and extracting it from the `tools/<framework>/<architecture>` directory.

.NET Core applications do not require this tool, as it is included with the `dotnet.exe` CLI. Any commands that reference `ef6.exe` can be replaced with `dotnet.exe ef`, although the arguments are slightly different. Refer to the [`dotnet ef` CLI documentation](https://docs.microsoft.com/en-us/ef/core/miscellaneous/cli/dotnet) for more information on the specific arguments.

::: {.attention .best-practice}
Prior to EF v6.3.0, the tool [`migrate.exe`](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/migrate-exe) was used to perform migrations. Refer to the linked documentation for the arguments.
:::

For ease of deployment, we recommend capturing the `ef6.exe` tool into a database build artifact alongside the DLL that contains the migrations. This can be accomplished in a build plan like this:

```
NuGet::Restore-Packages
(
    Target: ~\src\MyProject.Data
);

Copy-Files ef6.exe
(
    From: ~\src\packages\EntityFramework.<version>\tools\<framework>\<architecture>
    To: ~\src\MyProject.Data
);

Create-Artifact Database
(
    From: ~\src\MyProject.Data
);
```

Alternatively, you could store `ef6.exe` on the build server in a directory referenced in the Windows PATH. This is likely the best solution if all the migrations are executed on the same server (regardless of the actual server the database is on or specified in the connection string).

## Performing Migrations with `ef6.exe` {#migrations data-title="Performing Migrations"}

To migrate a database from a compiled DLL that contains code-first migrations, an example command would be:

```
ef6.exe database update --assembly MyProject.Data.dll --connection-string "Database=MyProject;Server=us-db-stg-01;Integrated Security=true"
```

This command will run all migrations in the `MyProject.Data.dll` assembly against the `MyProject` database. An optional `--target` migration name may specified, which will only run migrations up to and including the specified one by name.

## Performing Migrations with BuildMaster {#buildmaster data-title="Migrations in BuildMaster"}

To perform a migration in a BuildMaster deployment plan, simply use the `Exec` operation against `ef6.exe`. Assuming it was captured in an artifact, the deployment plan only requires the following operations:

```
Deploy-Artifact Database();

Exec ef6.exe database update --assembly MyProject.Data.dll --connection-string "Database=MyProject;Server=us-db-stg-01;Integrated Security=true";
```

While this example specifies the connection string directly, the connection string can also be stored as a [configuration variable](/docs/buildmaster/administration/configuration-variables), in a [configuration file](/docs/buildmaster/deployments/configuration-files), or omitted if the artifact already contains a configuration file (or one was deployed with `Deploy-ConfigFile`) with a valid connection string.