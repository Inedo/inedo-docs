---
title: "Upgrading Docker Containers"
order: 2
---

::: (INFO)
Before performing an upgrade to a new major version, we strongly recommend [backing up your Inedo product's database](/docs/installation/backing-up-restoring).
::: 

Upgrading to a new version of your Inedo product using Docker is easy. The container is designed to automatically upgrade your database as necessary when it is first started, so generally all you need to do is stop the old container, delete or rename it, and pull and start the new image's version.

This is the general procedure for upgrading an Inedo product.

1. Pull the desired version of the container image
2. Stop the current running container
3. Rename the container to `*-old`
4. Run the new version using the same arguments as before (see [Docker Installation Guide](http://localhost:5099/docs/installation/linux/docker-guide))
5. Remove the `*-old` container

### Example: ProGet 2025 Upgrade Script

In general, you should avoid using the `latest` tag, and simply enter the latest version. For example, to upgrade to ProGet 2025.13 you'd want to enter `25.0.13`.

```shell
docker pull proget.inedo.com/productimages/inedo/proget:25.0.13

docker stop proget

docker rename proget proget-old

docker run -d --name=proget --restart=unless-stopped \
  -v ./proget-packages:/var/proget/packages \
  -v ./proget-database:/var/proget/database  \
  -v ./proget-backups:/var/proget/backups \
  -p 8624:80  \
  proget.inedo.com/productimages/inedo/proget:25.0.13

docker rm proget-old
```

Note that, if the container fails to start due to incorrect arguments or other issues, you can simply rollback use these commands:

```shell
docker stop proget

docker rm proget

docker rename proget-old proget

docker start proget
```

## Upgrading the Database Only (Optional)
:::(Warning)
This only applies to ProGet, but we will add support to BuildMaster and Otter.
:::

The ProGet container will automatically upgrade the database when starting up; this upgrade might take a few minutes, which may appear to cause delays to monitoring processes like [Kubernetes probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes).

If you  run the following command in the ProGet container instead of the normal starting process, the database will be updated, and then the process will exit.

```
proget.exe upgradedb
```

Or the following in the docker image:

```
/usr/local/proget/service/proget upgradedb
```

There's some examples on [QA#3410](https://forums.inedo.com/topic/3140/proget-manual-database-upgrade-docker-kubernetes) for how to use this with Kubernetes.
