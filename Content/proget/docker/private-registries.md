---
title: "Private Docker Registry"
order: 2
---

​Using ProGet's Docker Registries, you manage your own and third-party Docker images in a unified way while defining fine-grained access control. ProGet works transparently with Docker Client, allowing images to be created internally or downloaded from remote Docker resources such as Docker Hub.

Internally, Docker registrations are represented as feeds, which means:

 - feed-specific permissions can be applied to Docker registries
 - you can configure Amazon S3 and Azure Blob Storage
 - the Docker registry name cannot be the same as the name of another feed

## Multiple Docker Registries

ProGet lets you define as many Docker registries as you want. This allows you to manage each project in its own registry and exercise better access control over your Docker images.

## Creating and using a Docker Registries in ProGet 

To create a Docker registry in ProGet, go to Containers > Create New Docker Registry, then enter a registry name.

:::(ERROR)
By default, Docker requires an SSL connection. You will either need to configure a valid SSL certificate (see [HTTPS Support on Windows](/docs/installation/windows/web/installation-windows-https-support) and [HTTPS Support on Linux](/docs/installation/linux/https-support)) or configure your Docker client to use an [insecure registry](#insecure).
:::

<h3 id="http-registries">Using an Insecure Registry (HTTP not HTTPS or Self-signed Certificates)</h3>

By default, the Docker client requires a Docker registry to use SSL with a valid certificate unless you use `localhost:<PORT>` as the URL. This would require ProGet to be set up in IIS with a valid security certificate. If you have set up ProGet using HTTP (in IIS or using the built-in web server) or are using a self-signed certificate in IIS, you must set up ProGet as an [insecure registry](https://docs.docker.com/registry/insecure/) in your Docker client.

#### HTTP Registries (IIS or Integrated Web Server)

To set up your Docker client to work with a registry using HTTP, you will need to add the registry's base URL name (not including the registry name) to the Docker `daemon.json` file.  If your URL is not using port `80` or does not contain a `.` (as when using only a server name), you will also need to include the port in your URL.

For Example:
- `"insecure-registries" : ["myregistrydomain.com:80"]`
- `"insecure-registries" : ["prodserver.inedo.local"]`
- `"insecure-registries" : ["prodserver:80"]`

An example Docker `daemon.json`:

```
{
  "registry-mirrors": [],
  "insecure-registries": [
    "myregistrydomain.com:80"
  ],
  "debug": false,
  "experimental": false
}
```

#### Self-signed  Certificates

To set up your Docker client to work with a registry using HTTPS with a self-signed certificate, you will need to install those certificates locally.  See Docker's documentation for [using self-signed certificates](https://docs.docker.com/registry/insecure/#use-self-signed-certificates) on how to properly install these certificates.

### Setting up Docker Client

ProGet supports both token-based authentication (requires Docker 1.11.0 or later) and HTTP Basic authentication.

First, you need to log in to Docker. To do this, use the following command, where _progetsv1_ is the name of your ProGet server:

    [~]$ docker login progetsv1:80

Any username and password that work for logging in via ProGet's web interface should also work with this command. Additionally, the username `api` can be used with an API key can be used as a password.

Docker requires TLS on any domain other than localhost, so the ProGet server must be accessible over HTTPS with a valid certificate. Alternatively, the ProGet server can be set up as an [insecure registry](#insecure) using HTTP or HTTPS with a self-signed certificate.

:::(INFO)
If the URL is not using port "`80`" or does not contain a "`.`" (i.e. using only a server name), you will also need to include the port in the URL.
:::

For Example:
- This will **NOT** work
   - This URL will tell docker to try and pull from the docker hub
    ```
    docker pull prodserver/myimages/dotnet/aspnet:5
    ```
- What will work:
   - This will use the server `prodserver.inedo.local`
    ```
    docker pull prodserver.inedo.local/myimages/dotenet/aspnet:5
    ```
   - This will use the server `prodserver` over port `80`
    ```
    docker push prodserver:80/myimages/dotnet/aspnet:5
    ```
   - This will use the server `prodserver` over port `443`
    ```
    docker push prodserver:443/myimages/dotnet/aspnet:5
    ```

Note that the current user must have permission to use Docker. On Linux, this means being a member of the `docker` group or using `sudo` or `su` to switch to the `root` user temporarily. On Windows, the cmd or PowerShell instance must be started with admin privileges.

## Publishing an Image 

To publish a Docker image to ProGet, you first need to **tag** the image using Docker in a special format. For example, if we have an image locally called `ubuntu:trusty`, we need to retag it as follows:

    [~]$ docker tag ubuntu:trusty progetsv1:443/dmc/ubuntu:trusty

Or, more generally, the format is: _server/feed/image:tag_.

Once tagged, the image can now be pushed to ProGet:

    [~]$ docker push progetsv1:443/dmc/ubuntu:trusty

### Pulling in Images

You can then pull the image in the same fashion:

    [~]$ docker pull progetsv1:443/dmc/ubuntu:trusty
    

## Deleting Images 

To delete images, you can use Docker's [Delete Image API](https://docs.docker.com/registry/spec/api/#deleting-an-image), using a HTTP DELETE request as follows:

    DELETE /v2/<repository-name>/manifests/<reference>

Note that the `reference` must be a digest. Deleting a tag is not allowed via this API, but deleting a manifest will remove any tag that directly refers to it.

In ProGet, the digest is at the top of the image details for a specific tag.

Via the API, the manifest digest can be found for tagged images:

* To get a list of repositories, request `https://proget.example.com/v2/_catalog`
* For each repository, you can get a list of tags via `https://proget.example.com/v2/main/library/node/tags/list`
* From there, the *tagged* manifests can be accessed via `https://proget.example.com/v2/main/library/node/manifests/latest`
* The hash is in the `Docker-Content-Digest` header for the response

You can also get the digest of an image you have locally via `docker inspect -f '{{.Id}}' proget.example.com/main/library/node:latest`

When building, pushing, or pulling an image, the digest is printed near the end of the command output. The build command also supports using `--iidfile` to save the digest to a text file.

## Using ProGet's Docker Registries Behind a Proxy Server 

Many users will use a proxy server (such as nginx or Apache) to project connections with ProGet to add SSL support or to help manage incoming connections. It is also recommended to use a proxy server when using the Docker-based version of ProGet. Sometimes this can cause Docker login issues, preventing users from logging in. If you are using a proxy, you need to set the `Web.BaseUrl` in ProGet's _Advanced Settings_ to the URL bound to your proxy server.  

When the Docker client attempts to authenticate with ProGet, a request is sent to the `/v2/` URL (i.e. `https://proget.server.com/v2/`).  ProGet will then return a header to tell Docker how to authenticate, that looks similar to `WWW-Authenticate: Bearer realm="https://proget.server.com/v2/_auth",service="proget.server.com"`. You must ensure that your proxy server properly sends this header back to the client with your proxy server's URL.

## Advanced Concepts
### Chunked and Monolithic Uploading

To support uploading large files over a single HTTP request, the Docker client will generally use a [chunked upload process](https://github.com/docker/distribution/blob/master/docs/spec/api.md#chunked-upload): send an upload initiation, a series of chunks (partial blob files), then an upload completion. ProGet supports this process, and assembles chunks as specified by the Docker API.

During the "FeedCleanUp" scheduled job, ProGet will purge incomplete uploads (i.e. chunks that were sent without an upload completion).

### Garbage Collection & Unreferenced Blobs

An "unreferenced blob" is a blob file that's known to ProGet (i.e. a database record and a file on disk) but has no Docker manifest that references the blob. An unreferenced blob isn't an error condition per se, and will generally occur if you upload a blob to ProGet but never upload a manifest that references the blob. For example, if you were to cancel a docker push halfway through, then you will have some unreferenced blobs. The "Docker Garbage Collection" Scheduled Job (Admin > Scheduled Jobs) runs nightly to clean up these unreferenced blobs.

#### Orphaned Files

Separately, there is also an "orphaned blob". This is basically a blob file that is unknown to ProGet (i.e. a file on disk without a database record). This is a rare error condition, as ProGet will raise an error if there was an error deleting the blob file. However, it's usually not a problem (just a few orphaned blob files here and there), so we do not currently have a tool/mechanism within ProGet to clean them up.

It's relatively easy to script an orphaned file cleanup. A ProGet user wrote a script that will do just that: https://gitlab.com/fhusson/proget-stuff/

### Connectors

As of ProGet v5.1, Docker feeds support [connectors](/docs/proget/feeds/connector-overview) to other docker registries. These connectors work a little differently than other connectors in ProGet. See [Docker connectors](/docs/proget/docker/connectors) for details.

### Layer Scanning
As of ProGet v2023.26, container images can be scanned for information such as operating system and used packages. When enabled, images are scanned on upload if they are small, or queued to be scanned in the background if they are large. Packages used by an image will be displayed in the Packages tab when viewing a Docker tag.

## Private Registry Limitations 

### Windows Integrated Authentication

The Docker client does not support built-in Windows authentication, which means you cannot set up "anonymous" access to a ProGet instance that has this enabled. To work around this, you can set up a second site in IIS without built-in Windows authentication enabled, pointing to the same path on disk.

### Other Limitations

Docker is designed for tight integration with the publicly hosted hub.docker.com. Private registries are supported to some extent, but the Docker client and associated tools always assume that you are using their public registry, or at least the official private [Docker Registry](https://docs.docker.com/registry) that they built and support. This can sometimes make working with private registries a bit difficult, but Docker Client support is gradually improving.
