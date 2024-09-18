---
title: "Docker Installation Guide"
order: 1
---

The easiest way to install ProGet, BuildMaster or Otter on Linux is to use Docker. Inedo product images are hosted on the official [Inedo Docker Registry](https://proget.inedo.com/feeds/ProductImages), and this guide walks you through running those container images.

### Docker Prerequisites
Docker must be installed and the Docker daemon must be running on your server. If you don't already have Docker installed, you can get [installation instructions for your specific Linux distribution from Docker](https://docs.docker.com/engine/installation/#installation).

Once Docker is running, you are ready to continue. Note that Docker commands usually need to be executed by members of the Docker group or with root/sudo privileges. So if you encounter errors with these commands, make sure your account is in the Docker group (`adduser myusername docker` and then log out and log back in), or issue them with sudo/su depending on your distro.

## Create a Network
First, you'll need to create a network for the SQL Server and Inedo Product containers to communicate. 

```Shell
docker network create inedo
```

## Create the Database
Inedo products requires an SQL Server database. You can either host this database externally or simply [use an SQL Server Docker image](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-configure-docker); it doesn't matter how it's hosted, as long as your instance can access it. 

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

#### Example: ProGet SQL Server Database
To create a database called `ProGet` on the SQL Server instance running in the **inedo-sql** container:

```Shell
docker exec -it inedo-sql /opt/mssql-tools/bin/sqlcmd \
  -S localhost -U SA -P '«YourStrong!Passw0rd»' \
  -Q 'CREATE DATABASE [ProGet] COLLATE SQL_Latin1_General_CP1_CI_AS'
```

<br>

::: (Info)( )
You can create the database however you want, but to avoid issues make sure you specify its collation as **SQL_Latin1_General_CP1_CI_AS**.
:::

## Starting your Inedo product' Docker Image 
Inedo product's Docker images contain a web server and a background service. To start your chosen image, use the `docker run` command. 

| Product | Image Name | Required Volumes |
| - | - | - |
| ProGet | `proget.inedo.com/productimages/inedo/proget` | `proget-artifacts` |
| BuildMaster | `proget.inedo.com/productimages/inedo/buildmaster` | `buildmaster-artifacts` |
| Otter | `proget.inedo.com/productimages/inedo/otter` | *none* |

If you'd like to get started right away with our defaults, you can just use the commands below, or continue reading for an explanation on the arguments and how to provide additional configuration values.  For root-less containers, check our our [Troublshooting Guide for root-less containers](/docs/installation/linux/installation-troubleshooting-docker-installations#root-less-containers).

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
### Configuration Parameters

|Parameter | Description |
| --- | --- |
| `-d` | Starts the container in detached mode. Without this argument, Docker will block your current terminal session and output the logs to your terminal. |
|`--name=«inedo-product»` | Names the container `«inedo-product»` so it can be easily referenced using other Docker commands. If you don't specify a name, Docker will generate one for you. |
| `--restart=unless-stopped` | Tells Docker to restart the container unless it is explicitly topped using Docker stop. This makes the container automatically restart after the host reboots. |
| `-p 80:80` | Exposes TCP port 80 of the container to port 80 of the host, so that browsers can access the web application. If you don't want to use port 80, you can change the first port number to whatever you would like; make sure to change the `BasedUrl` in Admin > Advanced Settings if the ports aren't the same. If you need to use a different port for the internal port (second port number), you will need to add the [ASPNETCORE_URLS](/docs/installation/linux/supported-environment-variables) environment variable. |
| `-p 443:443` | **ProGet v2022.18+ Only:** Exposes TCP port 443 of the container to port 443 of the host, so that browsers can access the web application using an HTTPS binding. If you don't want to use port 443, you can change the first port number to whatever you would like; make sure to change the `BasedUrl` in Admin > Advanced Settings if the ports aren't the same. You will need to add the [ASPNETCORE_URLS](/docs/installation/linux/supported-environment-variables) environment variable with an HTTPS binding like `https://*:443`. |
| `--net=inedo` | Putting the containers into a Docker network lets them see each other and prevents other Docker containers from accessing them. |
| `-v proget-packages:/var/proget/packages` | **ProGet Only:** Persists ProGet's packages in the proget-packages Docker volume. Note that a Docker volume is essentially a persistent, data-only container, and is the preferred mechanism for persisting data generated by a running Docker container. If you would prefer to mount a host file system directory directly instead, you may replace proget-packages with a host path, such as /var/proget/packages.|
| `-v /var/proget-ssl:/var/proget/ssl ` | **ProGet v2022.18+ Only:** Persists a host directory containing the certificates used in HTTPS bindings. Since these certificates are passed from the host to the container, a mount from the host file system directory directly should be used. |
| `-v buildmaster-artifacts:/var/buildmaster/artifacts` | **BuildMaster Only:** Persists the BuildMaster artifacts in the Docker volume buildmaster-artifacts. Note that a Docker volume is essentially just a persistent, data-only container and is the preferred mechanism for persisting data created by a running Docker container. If you prefer to directly mount a host file system directory instead, you can replace buildmaster-artifacts with a host path, such as /var/buildmaster/artifacts.|
| `-e` | This tells Docker to pass the proceding environment variables to the container. See the full list of [supported environment variables](/docs/installation/linux/supported-environment-variables). |
| `proget.inedo.com/productimages/inedo/«inedo-product»:latest` | This is the repository and tag for the Docker image. If you want to install a specific version, you can replace `latest` with that version number. Note that downgrades will only work if there have been no database schema changes. |

 ## Acquire a License Key
You'll need to have a valid license key once you get your Inedo product running. You can request a license key within the software itself, or use a license key that you already have, or request one from [my.inedo.com](https://my.inedo.com).
