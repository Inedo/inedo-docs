---
title: "HTTPS Support on Linux"
order: 5
---

HTTPS is increasingly becoming a requirement inside of many organizations, even for internal-only web applications. It's also required by several third-party client tools, such as NuGet and Docker.  

In this article, we'll provide two options for enabing HTTPS for ProGet, BuildMaster, and Otter when installed on Linux. For Windows-based installations, see [HTTPS Support on Windows](/docs/installation/windows/web/https-support).

## Configuring HTTPS with a Reverse Proxy { #reverse-proxy }
The simplest and recommended way to configure HTTPS is by using a reverse proxy to terminate TLS and forward requests to your Inedo product. This has the added benefit of allowing multiple websites and applications to be hosted on the same IP address.

Any HTTP reverse proxy can work, but most users find [NGINX](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/) to be the easiest. Other options include [Cady](https://caddyserver.com/), [Apache Mod_Proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html), and [HAProxy](https://www.haproxy.com/blog/the-four-essential-sections-of-an-haproxy-configuration/).

### Example: ProGet NGINX Config

```
server {
	listen 80;
	listen [::]:80;

	server_name "";

	return 301 https://`$http_host`$request_uri;
}
server
{
   listen 443 ssl;
   listen [::]:443 ssl;

   ssl_certificate /etc/ssl/cert/your_cert_com.crt;
   ssl_certificate_key /etc/ssl/cert/your_cert_com.key;

   server_name proget.domain.com;

   access_log /var/log/nginx/nginx.vhost.access.log;
   error_log /var/log/nginx/nginx.vhost.error.log;

   # Disable any limits to avoid HTTP 413 for large image uploads
   # ProGet requirement for VS to publish to ProGet 
   client_max_body_size 0;
   # required to avoid HTTP 411: see Issue #1486 (https://github.com/dotcloud/docker/issues/1486)
   chunked_transfer_encoding on;

   location /
   {
       proxy_pass http://localhost:81;		# local server
       proxy_http_version 1.1;
       proxy_cache_bypass  $http_upgrade;

       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection "upgrade";
       proxy_set_header Host $http_host;
       proxy_set_header X-Real-IP $remote_addr;
       proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       proxy_set_header X-Forwarded-Proto $scheme;
       proxy_set_header X-Forwarded-Host $http_host;
       proxy_set_header X-Forwarded-Port $server_port;
   }
}
```

## Configuring HTTPS without a Reverse Proxy  { #native-ssl }
Instead of using a reverse proxy, you can configure HTTPS bindings directly in your Inedo Product. 

::: (Warning) (⚠️ This Configuration is Not Recommmended )
Configuring HTTPS without a reverse proxy is more complicated to configure, troubleshoot, and maintain. You should use a reverse proxy as described above.
:::

This requires you to mount a volume that contains your certificate files and specify your ".pem" file and ".key" file as environment variables in your Docker container. 

::: (INFO)
You will need to ensure that the ".pem" file also includes your certificate validation path.
:::

To enable an HTTPS binding on your Docker container, you will need the following parameters added to your `docker run` command:
- Mount the volume containing your certificates (ex: `-v /path/to/pem/proget-ssl:/var/proget/ssl`)
- Expose the SSL port 443 externally (ex: `-p 8625:443`)
- Change your binding URLs to include an HTTPS binding (ex: `-e ASPNETCORE_URLS='http://*:80;https://*:443' `)
- Specify your certificate file using the SSL_CERT_FILE environment variable  (ex: `-e SSL_CERT_FILE='ProGetCertificate.pem'`)
- Specify your key file usng the SSL_KEY_FILE environment variable (ex: `-e SSL_KEY_FILE='ProGetCertificateServer.key'`)

Here is an example Docker run command that will expose HTTPS bindings on port 8625:
```bash
docker run -d --name=proget --restart=unless-stopped \
-v /home/user/proget-ssl:/var/proget/ssl \
-v /home/user/proget-packages:/var/proget/packages \
-p 8624:80 \
-p 8625:443 \
--net=inedo \
-e ASPNETCORE_URLS='http://*:80;https://*:443' \
-e SSL_CERT_FILE='ProGetCertificate.pem' \
-e SSL_KEY_FILE='ProGetCertificateServer.key' \
-e SQL_CONNECTION_STRING='Data Source=inedo-sql; Initial Catalog=ProGet; User ID=sa; Password=«YourStrong!Passw0rd»' \
proget.inedo.com/productimages/inedo/proget:latest
```
