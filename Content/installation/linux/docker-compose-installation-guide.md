---
title: "Docker Compose Installation Guide"
order: 3
---

One Docker command manages a single image, but with Docker Compose, one command can manage multilple images. Docker Compose is a tool for defining and running multi-container Docker applications on a single server. Docker Compose leverages YAML-based configuration files to define the settings for multiple images in one file. An entire ProGet, BuildMaster, and/or Otter installation can be set up using Docker Compose.  This guide uses the configuration based on the [Docker installation guide](/docs/installation/linux/docker-guide) migrated to a [Docker Compose configuration file](#example-docker-compose-configuration-file).

## Prerequisites 

Docker must be installed and the Docker daemon running on your server. If you don't already have Docker installed, you can get [installation instructions for your specific Linux distribution from Docker](https://docs.docker.com/engine/installation/#installation).

Docker Compose must also be installed. If you don't already have Docker Compose installed, you can get [installation instructions for your specific Linux distribution from Docker](https://docs.docker.com/compose/install/).

::: (INFO) (NOTE)
Docker and Docker Compose commands generally have to be issued by members of the Docker group or with root/sudo privileges. If you encounter errors with these commands, make sure your account is in the Docker group (`adduser myusername docker` and then log out and back in) or you are issuing them with the appropriate sudo/su privilege.
:::

## Running ProGet 2025+ with the Embedded Database (PostgreSQL) { #postgres }

ProGet 2025 and later include an embedded database (PostgreSQL) in the same container as ProGet, which means ProGet only needs a single container to operate.  Unless you need to run another container, like nginx, you should host ProGet in a container [using the Docker run command](/docs/installation/linux/docker-guide#running-a-proget-2025-container).

In order to include ProGet 2025+ with the embedded database, add the following to the `services` section of your docker-compose.yml.

```yaml
  pg:
    image: proget.inedo.com/productimages/inedo/proget:latest
    container_name: proget
    restart: unless-stopped
    ports:
      - "8624:80"
    volumes:
      - ./proget-packages:/var/proget/packages
      - ./proget-database:/var/proget/database
      - ./proget-backups:/var/proget/backups
```

## Using the SQL Server Backend { #sql-server}

With the exception of ProGet 2025, Inedo products require a SQL Server database. This guide includes running SQL Server in a container.  To install using Docker Compose:

1.  Create and save your `docker-compose.yml` (see our [example compose file](#example-docker-compose-configuration-file))
2.  In a terminal, navigate to the folder you saved your `docker-compose.yml`
3.  Start the database image using `docker-compose up -d db`
4.  Create your database using a `docker exec` command (examples below).
5.  Start the remaining images using `docker compose up -d`

#### Example: Create ProGet Database
```
docker exec -it inedo-sql /opt/mssql-tools/bin/sqlcmd \
   -S localhost -U SA -P '<YourStrong!Passw0rd>' \
   -Q 'CREATE DATABASE [ProGet] COLLATE SQL_Latin1_General_CP1_CI_AS'
```

#### Example: Create BuildMaster Database
```
docker exec -it inedo-sql /opt/mssql-tools/bin/sqlcmd \
   -S localhost -U SA -P '<YourStrong!Passw0rd>' \
   -Q 'CREATE DATABASE [BuildMaster] COLLATE SQL_Latin1_General_CP1_CI_AS'
```

#### Example: Create Otter Database
```
docker exec -it inedo-sql /opt/mssql-tools/bin/sqlcmd \
   -S localhost -U SA -P '<YourStrong!Passw0rd>' \
   -Q 'CREATE DATABASE [Otter] COLLATE SQL_Latin1_General_CP1_CI_AS'
```


## Upgrading Using Docker Compose 

To upgrade ProGet, BuildMaster, or Otter that was set up using Docker Compose:

1. [Backup your database](/docs/installation/backing-up-restoring).
2. In a terminal, navigate to the folder where your `docker-compose.yml`. 
3. Update your Compose file to the new version of the ProGet, BuildMaster and/or Otter image.
4. Run the upgrade using `docker-compose up -d`.

Docker Compose will handle the difference in image versions by pulling the new image and restart it. The new image will automatically upgrade the database schema upon start of the image.

## Uninstalling Using Docker Compose

To uninstall ProGet, BuildMaster, or Otter that was set up using Docker Compose:

1. In a terminal, navigate to the folder where your `docker-compose.yml`. 
2. Uninstall using `docker-compose down`.

This will stop all images listed in your compose file, remove the containers, and remove the network defined in your compose file. This will *not* remove the locally cached images or the persisted files that were specified in your compose volumes.  You will need to manually remove these afterwards.

## Example Docker Compose Configuration File 

When creating your `docker-compose.yml` using this example, you will need to set your SQL password (`<YourStrong!Passw0rd>`) and the product's image version (`<version>`). You may also need to update the volumes to meet the needs of your environment.

### Base Docker Compose File 

This is a base docker compose file that includes the shared SQL Server instance (`inedo-sql`) and the shared `inedo` Docker network.  You will also need to include a product node ([ProGet](#quick-start-proget), [BuildMaster](#quick-start-buildmaster), or [Otter](#quick-start-otter)) in your compose file .

::: (INFO) (NOTE)
This example specifies the free SQL Express edition. This is adequate for most ProGet installations, but you can use any other edition, if you have the license for it.
:::

```yaml
version: '3.8'
services:
  db:
    image: mcr.microsoft.com/mssql/server:2019-latest
    container_name: inedo-sql
    restart: unless-stopped
    ports:
      - "1433:1433"
    networks:
      inedo:
        aliases:
          - inedo-sql
    environment:
      ACCEPT_EULA: Y
      MSSQL_SA_PASSWORD: <YourStrong!Passw0rd>
      MSSQL_PID: Express
# Optional if using persisted storage locations (https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-docker-container-configure?view=sql-server-ver15&pivots=cs1-bash#persist)
#    volumes:
#      - ./inedo-sql/data:/var/opt/mssql/data
#      - ./inedo-sql/log:/var/opt/mssql/log
#      - ./inedo-sql/secrets:/var/opt/mssql/secrets
networks:
  inedo:
    name: inedo
```

<h3 id="quick-start-proget">Quick Start: ProGet</h3>

This is an example ProGet node that can be inserted into the [base Docker Compose file](#example-docker-compose-configuration-file).  See the [supported environment variables guide](/docs/installation/linux/docker-guide#supported-environment-variables) for other configuration options.

```yaml
  pg:
    image: proget.inedo.com/productimages/inedo/proget:<version>
    container_name: proget
    restart: unless-stopped
    environment: 
      PROGET_SQL_CONNECTION_STRING: Data Source=inedo-sql; Initial Catalog=ProGet; User ID=sa; Password=<YourStrong!Passw0rd>
    ports:
      - "80:80"
    networks:
      inedo:
        aliases:
          - proget
# Update this path to persist your ProGet packages storage
    volumes:
      - ./proget-packages:/var/proget/packages
    depends_on:
      - db
```

<h3 id="quick-start-buildmaster">Quick Start: BuildMaster</h3>

This is an example BuildMaster node that can be inserted into the [base Docker Compose file](#example-docker-compose-configuration-file). See the [supported environment variables guide](/docs/installation/linux/docker-guide#supported-environment-variables) for other configuration options.

```yaml
  bm:
    image: proget.inedo.com/productimages/inedo/buildmaster:<version>
    container_name: buildmaster
    restart: unless-stopped
    environment: 
      BUILDMASTER_SQL_CONNECTION_STRING: Data Source=inedo-sql; Initial Catalog=BuildMaster; User ID=sa; Password=<YourStrong!Passw0rd>
    ports:
      - "80:80"
    networks:
      inedo:
        aliases:
          - buildmaster
# Update this path to persist your BuildMaster artifacts storage
    volumes:
      - ./buildmaster-artifacts:/var/buildmaster/artifacts
    depends_on:
      - db
```

<h3 id="quick-start-otter">Quick Start: Otter</h3>

This is an example Otter node that can be inserted into the [base Docker Compose file](#example-docker-compose-configuration-file). See the [supported environment variables guide](/docs/installation/linux/docker-guide#supported-environment-variables) for other configuration options.

```yaml
  ot:
    image: proget.inedo.com/productimages/inedo/otter:<version>
    container_name: otter
    restart: unless-stopped
    environment: 
      OTTER_SQL_CONNECTION_STRING: Data Source=inedo-sql; Initial Catalog=Otter; User ID=sa; Password=<YourStrong!Passw0rd>
    ports:
      - "80:80"
    networks:
      inedo:
        aliases:
          - otter
    depends_on:
      - db
```

