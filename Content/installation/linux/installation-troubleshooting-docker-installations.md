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

This happens because the internal port of the container uses ports 80 and 443 and most root-less containers do not allow ports less than 1024 even though it is internal to the container.  In order to fix this, you will need change the internal port that ProGet/BuildMaster/Otter is running on.  You can do this by setting the `ASPNETCORE_URLS` environment variable to map it to a new internal port:

```shell
  -e ASPNETCORE_URLS='http://*:8080;https://*:4343'
```

The above example will bind the internal ports to 8080 (HTTP) and 4343 (HTTPS). You will still need to map those ports externally, for example using `-p 8624:8080` and `-p 8625:4343` to expose 8624 (HTTP) and 8625 (HTTPS).

### Cannot connect to services that use a self-signed or an internal certificate authority
By default, all SSL based services that ProGet connects to requires a valid certificate.  On Windows, this is pretty simple to solve by just trusting the certificate in the Windows Certificate Manager.  On Linux/Docker, you need to mount the `/usr/local/share/ca-certificates` volume (ex: `-v self-signed-ca:/usr/local/share/ca-certificates`) and then place all your self-signed certificates or internal CA certificates and then restart your container.  Upon start of the container, ProGet will execute `update-ca-certificates` before starting ProGet which will include those certificates as a Trusted Authority.
