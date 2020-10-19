---
title: Docker Compose Installation Guide
sequence: 100
show-headings-in-nav: true
keywords: proget, installation, linux, docker, docker-compose
---

Docker Compose is a tool for defining and running multi-container Docker applications on a single server.  Compose leverages YAML based configuration files to define the settings for an application's services.  An entire ProGet installation can be setup using Docker Compose.

## Prerequisites {#prerequisites data-title="Prerequisites"}

Docker must be installed and the docker daemon running on your server. If you don't already have Docker installed, you can get [installation instructions for your specific Linux distribution from Docker](https://docs.docker.com/engine/installation/#installation).

Docker Compose must be installed.  If you don't already have Docker Compose installed, you can get [installation instructions for your specific Linux distribution from Docker](https://docs.docker.com/compose/install/).

Once Docker is up and running and Compose is installed, you are ready to continue. Note that Docker and Docker Compose commands generally have to be issued by members of the docker group or with root/sudo privileges, so if you encounter errors with these commands make sure your account is in the docker group (`adduser myusername docker` and then log out and back in) or you are issuing them with the appropriate sudo/su depending on your configuration.

## Installing ProGet using Docker Compose {#install data-title="Install using Compose"}

In this example, ProGet is installed using the same installation settings as the instance installed in the [Linux and Docker Installation Guide](/docs/proget/installation/installation-guide/linux-docker).

To install ProGet using Docker Compose:

1.  Create and save your `docker-compose.yml` (see our [example compose file](#example)).
2.  In a terminal, navigate to the folder you saved your `docker-compose.yml` to.
3.  Start the database image using `docker-compose up -d db`
4.  Create the ProGet database using the docker command
```
docker exec -it proget-sql /opt/mssql-tools/bin/sqlcmd \
   -S localhost -U SA -P '‹YourStrong!Passw0rd›' \
   -Q 'CREATE DATABASE [ProGet] COLLATE SQL_Latin1_General_CP1_CI_AS'
```
5. Start the remaining images using `docker compose up -d`

## To Upgrade ProGet Using Docker Compose {#upgrade data-title="Upgrade using Compose"}

To upgrade ProGet when setup using Docker Compose:

1. [Backup your database](/docs/proget/installation/backing-up)
2. In a terminal, navigate to the folder where your `docker-compose.yml` 
3. Update your Compose file to the new version of the ProGet Image
4. Run the upgrade using `docker-compose up -d`

Docker Compose will handle the difference in image versions and pull the new image, restart the ProGet image, and the new ProGet image will automatically upgrade the database schema upon start of the image.

## Uninstall The ProGet Instance (#uninstall data-title="Uninstall ProGet using Compose")

To uninstall ProGet when setup using Docker Compose:

1. In a terminal, navigate to the folder where your `docker-compose.yml` 
2. Run the upgrade using `docker-compose down`

This will stop all images listed in your compose file, remove the containers, and remove the network defined in your compose file.  This will not remove the locally cached images or the files persisted specified in your compose volumes.  You will need to manually remove these afterwards.

## An Example Docker Compose configuration file {#example data-title="Example docker-compose.yml"}

When creating your `docker-compose.yml` using this example, you will need to set your SQL password (`‹YourStrong!Passw0rd›`) and the ProGet version (`<ProGet Version>`).  You may also need to update the volumes to meet your environment.

**Note:** This example specifies the free SQL Express edition. This is adequate for most ProGet installations, but you can use any other edition as well if you have the license for it.

```docker-compose.yml
version: '3.8'
services:
  db:
    image: mcr.microsoft.com/mssql/server:2019-latest
    container_name: proget-sql
    restart: unless-stopped
    ports:
      - "1433:1433"
    networks:
      proget-network:
        aliases:
          - proget-sql
    environment:
      ACCEPT_EULA: Y
      MSSQL_SA_PASSWORD: ‹YourStrong!Passw0rd›
      MSSQL_PID: Express
# Optional if using persisted storage locations (https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-docker-container-configure?view=sql-server-ver15&pivots=cs1-bash#persist)
#    volumes:
#      - ./proget-sql/data:/var/opt/mssql/data
#      - ./proget-sql/log:/var/opt/mssql/log
#      - ./proget-sql/secrets:/var/opt/mssql/secrets
  pg:
    image: proget.inedo.com/productimages/inedo/proget:<ProGet Version>
    container_name: proget
    restart: unless-stopped
    environment: 
      SQL_CONNECTION_STRING: Data Source=proget-sql; Initial Catalog=ProGet; User ID=sa; Password=‹YourStrong!Passw0rd›
    ports:
      - "80:80"
    networks:
      proget-network:
        aliases:
          - proget
# Update this path to persist your proget packages storage
    volumes:
      - ./proget-packages:/var/proget/packages
    depends_on:
      - db
networks:
  proget-network:
    name: proget-network
```