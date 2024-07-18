---
title: "Upgrading Docker Containers"
order: 2
---

::: (INFO)
Before performing an upgrade to a new Major or Minor version, we strongly recommend [backing up your Inedo product's database](/docs/installation/backing-up-restoring).
::: 

Upgrading to a new version of your Inedo product using Docker is easy. The container is designed to automatically upgrade your database as necessary when it is first started, so generally all you need to do is stop the old container, delete or rename it, and pull and start the new image's version.

This is the procedure for upgrading an Inedo product, with example scripts to follow:

1. Stop the Inedo product's container
2. Rename the container to `<product>-old`
3. Pull the new version of the container image
4. Run the new version with `--volumes-from=<product>-old`
5. Remove the `*-old` container

### ProGet Upgrade Script

```Shell
docker stop proget

docker rename proget proget-old

docker pull proget.inedo.com/productimages/inedo/proget:«new-version»

docker run -d --volumes-from=proget-old \
    -p 80:80 --net=inedo \
    --name=proget --restart=unless-stopped \
    -e SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=ProGet; User ID=sa; Password=«YourStrong!Passw0rd»' \
    proget.inedo.com/productimages/inedo/proget:«new-version»

docker rm proget-old
```

### BuildMaster Upgrade Script
```Shell
docker stop buildmaster

docker rename buildmaster buildmaster-old

docker pull buildmaster.inedo.com/productimages/inedo/buildmaster:«new-version»

docker run -d --volumes-from=buildmaster-old \
    -p 80:80 --net=inedo \
    --name=buildmaster --restart=unless-stopped \
    -e SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=BuildMaster; User ID=sa; Password=«YourStrong!Passw0rd»' \
    buildmaster.inedo.com/productimages/inedo/buildmaster:«new-version»

docker rm buildmaster-old
```

### Otter Upgrade Script
```Shell
docker stop otter

docker rename otter otter-old

docker pull otter.inedo.com/productimages/inedo/otter:«new-version»

docker run 
    -p 80:80 --net=inedo \
    --name=otter --restart=unless-stopped \
    -e SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=Otter; User ID=sa; Password=«YourStrong!Passw0rd»' \
    otter.inedo.com/productimages/inedo/otter:«new-version»

docker rm otter-old
```

## Upgrading the Database Only (Optional)
:::(Warning)
This only applies to ProGet, but we will add support to BuildMaster and Otter.
:::

The ProGet container will automatically upgrade the database when starting up; this upgrade might take a few minutes, which may appear to cause delays to monitoring processes like [Kubernetes probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes).

If you  run the following command in the ProGet container instead of the normal starting process, the database will be updated, and then the process will exit.

```
ProGet.Service.exe upgradedb
```

Or the following in the docker image:

```
/usr/local/proget/service/ProGet.Service upgradedb
```

There's some examples on [QA#3410](https://forums.inedo.com/topic/3140/proget-manual-database-upgrade-docker-kubernetes) for how to use this with Kubernetes.
