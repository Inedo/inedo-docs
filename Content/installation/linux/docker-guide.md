---
title: "Docker Installation Guide"
order: 1
---

The easiest way to install ProGet, BuildMaster or Otter on Linux is to use Docker. Inedo product images are hosted on the official [Inedo Docker Registry](https://proget.inedo.com/feeds/ProductImages), and this guide walks you through running those container images.

### Docker Prerequisites
Docker must be installed and the Docker daemon must be running on your server. If you don't already have Docker installed, you can get [installation instructions for your specific Linux distribution from Docker](https://docs.docker.com/engine/installation/#installation).

Once Docker is running, you are ready to continue. Note that Docker commands usually need to be executed by members of the Docker group or with root/sudo privileges. So if you encounter errors with these commands, make sure your account is in the Docker group (`adduser myusername docker` and then log out and log back in), or issue them with sudo/su depending on your distro.

## Running a ProGet 2025+ Container { #postgres }

ProGet 2025+ can be run as a single Docker container on your server using the following command:

```bash
docker run -d --name=proget --restart=unless-stopped \
  -v ./proget-packages:/var/proget/packages \
  -v ./proget-database:/var/proget/database  \
  -v ./proget-backups:/var/proget/backups \
  -p 8624:80  \
  proget.inedo.com/productimages/inedo/proget:latest
```

That command uses common default values and will create three directories (`proget-packages`, `proget-database`, `proget-backups`) in the current working directory for the [specified volumes](#supported-volumes).

You can then access ProGet in your web browser with `http://«your-server»:8624/`. Note that this is *not* an HTTPS url, and many client tools (npm, NuGet, etc) will issues warnings or simply refuse to connect. See [HTTPS Support on Linux](/docs/installation/linux/https-support) to learn more.


## Using the SQL Server Backend { #sql-server }

With the exception of ProGet 2025, Inedo products require a SQL Server database. You can either host this database externally or simply run a SQL Server container; it doesn't matter how it's hosted, as long as your instance can access it. 

### Running a SQL Server Docker Container

If you don't have an external SQL Server, you will need to run a container from the [SQL Server Docker image](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-configure-docker)

#### 1. Create a Network
First, you'll need to create a network for the SQL Server and Inedo Product containers to communicate. 

```Shell
docker network create inedo
```

#### 2. Create the Database

To start an SQL Server container on the `inedo` network you created, use this command:

```Shell
docker run --name inedo-sql \
  -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=«YourStrong!Passw0rd»' \
  -e 'MSSQL_PID=Express' --net=inedo --restart=unless-stopped \
  -d mcr.microsoft.com/mssql/server:2019-latest
```

:::(Info) ()
In this example, the free SQL Express edition is specified. This is sufficient for most installations, but you can use any other edition if you have the license for it.
:::

Once you have an SQL Server instance running, you'll need to create an empty database. 

##### Example: ProGet SQL Server Database
To create a database called `ProGet` on the SQL Server instance running in the **inedo-sql** container:

```Shell
docker exec -it inedo-sql /opt/mssql-tools18/bin/sqlcmd \
  -C -S localhost -U SA -P '«YourStrong!Passw0rd»' \
  -Q 'CREATE DATABASE [ProGet] COLLATE SQL_Latin1_General_CP1_CI_AS'
```

<br>

::: (Info)
You can create the database however you want, but to avoid issues make sure you specify its collation as **SQL_Latin1_General_CP1_CI_AS**.
:::

### Running the Inedo Product Container with SQL Server

Inedo product's Docker images contain a web server and a background service. To start your chosen image, use the `docker run` command. 

| Product | Image Name | Required Volumes |
| - | - | - |
| ProGet | `proget.inedo.com/productimages/inedo/proget` | `proget-packages` |
| BuildMaster | `proget.inedo.com/productimages/inedo/buildmaster` | `buildmaster-artifacts` |
| Otter | `proget.inedo.com/productimages/inedo/otter` | *none* |

If you'd like to get started right away with our defaults, you can just use the commands below, or continue reading for an explanation on the arguments and how to provide additional configuration values.  For root-less containers, check our our [Troubleshooting Guide for root-less containers](/docs/installation/linux/installation-troubleshooting-docker-installations#root-less-containers).

### Quick Start: ProGet
```Shell
docker run -d --name=proget --restart=unless-stopped \
  -v proget-packages:/var/proget/packages -p 80:80 --net=inedo \
  -e PROGET_SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=ProGet; User ID=sa; Password=«YourStrong!Passw0rd»' \
  proget.inedo.com/productimages/inedo/proget:latest
```

### Quick Start: BuildMaster
```Shell
docker run -d --name=buildmaster --restart=unless-stopped \
  -v buildmaster-artifacts:/var/buildmaster/artifacts \
  -p 80:80 --net=inedo \
  -e BUILDMASTER_SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=BuildMaster; User ID=sa; Password=«YourStrong!Passw0rd»' \
  proget.inedo.com/productimages/inedo/buildmaster:latest
```

### Quick Start: Otter
```Shell
docker run -d --name=otter --restart=unless-stopped \
  -p 80:80 --net=inedo \
  -e OTTER_SQL_CONNECTION_STRING ='Data Source=inedo-sql; Initial Catalog=Otter; User ID=sa; Password=«YourStrong!Passw0rd»' \
  proget.inedo.com/productimages/inedo/otter:latest
  ```


## Supported Volumes

Volumes are used to store data outside of the running container that will persist after the container is stopped or removed.

### Package Store Volume  (ProGet Only)
ProGet requires a `/var/proget/packages` volume to specified, which will be the default storage location for all content (packages, container images, assets) in ProGet. This should be backed up regularly.

### Embedded Database Volumes (ProGet 2025+ Only)

When using the default, [embedded PostgreSQL database backend](/docs/installation/postgresql), you will need to specify a `/var/proget/database` volume to store the database itself and a `/var/proget/backups` volume to specify backups of the database.

These should be backed up regularly.

### Artifacts Volume (BuildMaster Only)
BuildMaster requires a `/var/buildmaster/artifacts` volume to be specified, which will be used to store all build artifacts you create and intend to deploy. This should be backed up regularly.

### Other Volumes

These volumes are not required or commonly used.

* `/var/«inedo-product»/extensions` stores [custom extensions](/docs/inedosdk)
* `/usr/local/share/ca-certificates` store the certificates to be included in the container's certificate authority, which may be required when your Inedo product needs to connect to a server with self-signed certificates 
* `/var/«inedo-product»/ssl` store the certificates used in [native SSL bindings](/docs/installation/linux/https-support), which is not a recommended configuration
* `/var/«inedo-product»/logs` stores the [HTTP request log](/docs/installation/logging/http-request-logging) files


## Supported Environment Variables

Environment variables are key-value pairs that are specified when running the container and allow you to supply configuration settings for your Inedo product.

### Database Connection String

If you use a [SQL Server Backend](#sql-server), you will need to specify a connection string with the `«inedo-product»_SQL_CONNECTION_STRING` environment variable. For example:

```bash
-e PROGET_SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=ProGet; User ID=sa; Password=«YourStrong!Passw0rd»'
```

For the [non-embedded PostgreSQL database backend](/docs/installation/postgresql), you will need to specify a `«inedo-product»_POSTGRES_CONNECTION_STRING` environment variable instead.

Either connection string may also be specified as a [Docker secret file](https://docs.docker.com/engine/swarm/secrets/), by appending `_FILE` to the variable name. For example:

```
-e PROGET_SQL_CONNECTION_STRING_FILE='/home/proget/secrets/proget_connection_string'
```

Note that the Docker secret must be set first and "called" within the stack file. 

### Encryption Key

Inedo products can use a 32-character hex encryption key specified in the `«inedo-product»_ENCRYPTION_KEY` variable to store some secrets in the database and encrypt session cookies. For example:

```
-e PROGET_ENCRYPTION_KEY='37D27A670394F7D82CE57F1F07D69747'
```

You can generate a random 32-character key using `head -c16 /dev/urandom | xxd -p -c32`. 

You can also specify the encryption key as a Docker secret using the `«inedo-product»_ENCRYPTION_KEY_FILE` variable instead.

### Other Environment Variables

The `TZ` environment variable can be used to specify the timezone of the container and set to any [TZ database name](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) 
```
-e TZ='America/Denver'
```

The `SSL_CERT_FILE`, `SSL_KEY_FILE`, `SSL_PASSWORD`, and `SSL_ALLOW_INVALID` environment variables are used to configure [native SSL support](/docs/installation/linux/https-support), which is not a recommend configuration.

The `ASPNETCORE_URLS` variable can override the default listening ports (80, 443) in the container, which may be useful working with rootless containers. For example:
```
-e ASPNETCORE_URLS='http://*:8181;https://*:4443'
```

See our [Troubleshooting Guide for rootless containers](/docs/installation/linux/installation-troubleshooting-docker-installations#root-less-containers) to learn more. 

The `ENABLE_REQUEST_LOGGING` environment variable can be set to `true` to enable [HTTP request logging](/docs/installation/logging/http-request-logging). For example:
```
-e ENABLE_REQUEST_LOGGING='true'
```