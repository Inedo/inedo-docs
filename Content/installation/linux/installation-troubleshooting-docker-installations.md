---
title: "Troubleshooting Docker Installations"
order: 6
---

If your Docker-hosted Inedo product isn't working, or isn't accessible, try some of these troubleshooting steps to determine what's wrong.

### View Running Containers
Run `docker ps` to display all of the currently running Docker containers. If you don't see your Inedo Product listed, then the container isn't running and you can issue a `docker logs «inedo-product»` command to display the log messages from when it last ran; there was most likely some kind of initialization error that prevented it from starting up.

### View Container Logs
Run `docker logs «inedo-product»` to display the log messages from your Inedo Product. Look for any error or warning messages for clues as to what's wrong.

### Check Firewall
Make sure a firewall on the host server isn't blocking requests on the specified port.

### Restart Container
Run `docker restart «inedo-product»` to stop the container and then restart it.

<h3 id="root-less-containers">Root-less containers</h3>

When running a root-less container tool like [Podman](https://podman.io/) or [Heroku](https://www.heroku.com/) you may experience an error similar to `Unable to start Kestrel. System.Net.Sockets.SocketException (13): Permission denied while running alpine as non root user`.  

This happens because the internal port of the container uses port 80 and most root-less containers do not allow ports less than 1024 even though it is internal to the container.  In order to fix this, you will need change the internal port that ProGet/BuildMaster/Otter is running on.  Here is an example to change it to port 8080.

#### For ProGet:
1. Create a custom config and map the container to that folder:
```shell
echo '<?xml version="1.0" encoding="utf-8"?><InedoAppConfig><ConnectionString Type="SqlServer">'"`$SQL_CONNECTION_STRING"'</ConnectionString><WebServer Enabled="true" Urls="http://*:8080/"/></InedoAppConfig>' > /usr/share/Inedo/SharedConfig/ProGet.config
```
2. Run the container with the binding to the new internal port
```shell
docker run -d --name=proget --restart=unless-stopped \
  -v proget-packages:/var/proget/packages -p 8080:8080 --net=proget \
  -e SQL_CONNECTION_STRING='Data Source=proget-sql; Initial Catalog=ProGet; User ID=sa; Password=«YourStrong!Passw0rd»'' \
  proget.inedo.com/productimages/inedo/proget:latest
```

#### For Otter and BuildMaster:
Otter and BuildMaster have an environment variable to specify the internal URL.  You simply need to run add an environment variable and map the new internal port:

BuildMaster:
```shell
docker run -d --name=buildmaster --restart=unless-stopped \
  -v buildmaster-artifacts:/var/buildmaster/artifacts \
  -p 8080:8080 --net=inedo \
  -e BUILDMASTER_SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=BuildMaster; User ID=sa; Password=«YourStrong!Passw0rd»' \
  -e ASPNETCORE_URLS="http://*:8080" \
  proget.inedo.com/productimages/inedo/buildmaster:latest
```
Otter
```shell
docker run -d --name=otter --restart=unless-stopped \
  -p 8080:8080 --net=inedo \
  -e BUILDMASTER_SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=Otter; User ID=sa; Password=«YourStrong!Passw0rd»' \
  -e ASPNETCORE_URLS="http://*:8080" \
  proget.inedo.com/productimages/inedo/otter:latest
```

