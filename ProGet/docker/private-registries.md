---
title: Private Docker Registries in ProGet
subtitle: Private Docker Registry
sequence: 200
show-headings-in-nav: true
---

Using ProGet's Docker Registries, you can manage your own and third-party Docker images in a uniform manner, while defining fine-grained access control. ProGet works transparently with the Docker client, with images created internally or downloaded from remote Docker resources such as Docker Hub.

Internally, Docker registries are represented as feeds, which means:

 - feed-scoped privileges can be applied on Docker registries
 - you can configure Amazon S3 and Azure blob storage
 - the Docker registry name cannot be the same as another feed name

## Multiple Docker Registries

ProGet lets you define as many Docker registries as you wish. This enables you to manage each project in a distinct registry and exercise better access control over your Docker images.

## Creating and using a Docker Registries in ProGet {#using-registries data-title="Using Docker Registries"}

To create a Docker registry in ProGet, go to Containers > Create New Docker Registry, then enter a registry name.

:::attention {.technical}
![](/resources/images/icons/technical.png)
By default, Docker requires an SSL connection.  You will either need to configure ProGet to use IIS with a valid SSL certificate or configure your Docker client to use an [insecure registry](#insecure).
:::

### Using an Insecure Registry (HTTP not HTTPS or Self-signed Certificates) {#insecure data-title="Using insecure registries (HTTP or Self-signed)"}

By default, the Docker client requires a Docker registry to use SSL with a valid certificate if not using `localhost:<PORT>` as your URL. This would require ProGet to be setup in IIS with a valid security certificate. If you have ProGet setup using HTTP (in IIS or using the Integrated Web Server) or you are using a self-signed certificate in IIS, you must setup ProGet as an [insecure registry](https://docs.docker.com/registry/insecure/) in your Docker client.

#### HTTP Registries (IIS or Integrated Web Server)

To setup your Docker client to work with a registry using HTTP, you will need to add the registry's base URL name (not including the registry name) to the Docker `daemon.json` file.  If your URL is not using port `80` or does not contain a `.` (like when using only a server name), you will also need to include the port in your URL.

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

To setup your Docker client to work with a registry using HTTPS with a self-signed certificate, you will need to install those certificates locally.  See Docker's documentation for [using self-signed certificates](https://docs.docker.com/registry/insecure/#use-self-signed-certificates) on how to properly install these certificates.

### Setting up Docker Client

ProGet supports both token-based authentication (requiring [Docker 1.11.0](https://github.com/moby/moby/blob/master/CHANGELOG.md#1110-2016-04-13) or newer) and HTTP Basic authentication.

First, you will need to log in to Docker. To do this, use the following command, where _progetsv1_ is the name of your ProGet server:

    [~]$ docker login progetsv1:80

Any username and password that work to log in via ProGet's web interface should work using this command. Additionally, the username `api` can be used with an API key as the password.

Docker requires TLS on any domain other than localhost, so the ProGet server must be accessible over HTTPS with a valid certificate. Alternatively, the ProGet server can be setup as an [insecure registry](#insecure) using HTTP or HTTPS with a self-signed certificate.

:::attention {.best-practice}
If your URL is not using port "`80`" or does not contain a "`.`" (i.e. using only a server name), you will also need to include the port in your URL.
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

Note that the current user must have permission to use Docker. On Linux, this means being a member of the `docker` group or using `sudo` or `su` to switch to the `root` user temporarily. On Windows, the cmd or powershell instance must be started with admin privileges.

## Publishing an Image {#publishing data-title="Publishing Container Images"}

To publish a Docker image to ProGet, you first need to **tag** the image using Docker in a special format. For example, if we have an image locally called `ubuntu:trusty`, we need to retag it as follows:

    [~]$ docker tag ubuntu:trusty progetsv1:443/dmc/ubuntu:trusty

Or, more generally, the format is: _server/feed/image:tag_.

Once tagged, the image can now be pushed to ProGet:

    [~]$ docker push progetsv1:443/dmc/ubuntu:trusty

### Pulling in Images

You can then pull the image in the same fashion:

    [~]$ docker pull progetsv1:443/dmc/ubuntu:trusty
    

## Deleting Images  {#deleting data-title="Deleting Container Images"}

To delete images, you can use Docker's [Delete Image API](https://docs.docker.com/registry/spec/api/#deleting-an-image), using a HTTP DELETE request as follows:

    DELETE /v2/<repository-name>/manifests/<reference>

Note that the `reference` must be a digest. Deleting a tag is not allowed via this API, but deleting a manifest will remove any tag that refers directly to it.

In ProGet, the digest is at the top of the image details for a specific tag.

Via the API, the manifest digest can be found for tagged images:

* To get a list of repositories, request `https://proget.example.com/v2/_catalog`
* For each repository, you can get a list of tags via `https://proget.example.com/v2/main/library/node/tags/list`
* From there, the *tagged* manifests can be accessed via `https://proget.example.com/v2/main/library/node/manifests/latest`
* The hash is in the `Docker-Content-Digest` header for the response

You can also get the digest of an image you have locally via `docker inspect -f '{{.Id}}' proget.example.com/main/library/node:latest`

When building, pushing, or pulling an image, the digest is printed near the end of the command output. The build command also supports using `--iidfile` to save the digest to a text file.

## Using ProGet's Docker Registries Behind a Proxy Server {#proxy data-title="Using a Proxy"}

Many users will use a proxy server (like nginx or Apache) to proxy connections with ProGet to add SSL support or help manage incoming connections.  It is also recommended to use a proxy server when using the Docker based version of ProGet.  Sometimes this may cause Docker login issues causing users to not be able to log in.  When using a proxy, you will need to set the `Web.BaseUrl` in ProGet's _Advanced Settings_ to the URL bound to your proxy server.  

When the Docker client attempts to authenticate with ProGet, a request is sent to the `/v2/` URL (i.e. `https://proget.server.com/v2/`).  ProGet will then return a header to tell Docker how to authenticate that looks similar to `WWW-Authenticate: Bearer realm="https://proget.server.com/v2/_auth",service="proget.server.com"`.  You will need to make sure that your proxy server properly sends this header back to the client with the URL of you proxy server in it.

## Advanced Concepts {#advanced data-title="Advanced Concepts"}
### Chunked and Monolithic Uploading

To support uploading large files over a single HTTP request, the Docker client will generally use a [chunked upload process](https://github.com/docker/distribution/blob/master/docs/spec/api.md#chunked-upload): send an upload initiation, a series of chunks (partial blob files), then an upload completion. ProGet supports this process, and assembles chunks as specified by the Docker API.

During the "FeedCleanUp" scheduled job, ProGet will purge incomplete uploads (i.e. chunks that were sent without an upload completion).

### Garbage Collection

Unlike packages, a Docker image is not self-contained: it is a reference to a manifest blob, which in turn references a number of layer blobs. These layer blobs may be referenced by other manifests in the registry, which means that you can't simply delete referenced layer blobs when deleting a manifest blob.

This is where garbage collection comes in; it's the process of removing blobs from the package store when they are no longer referenced by a manifest. ProGet performs garbage collection on Docker registries through the "FeedCleanUp" scheduled job.

### Connectors

As of ProGet v5.1, Docker feeds support [connectors](/docs/proget/core-concepts/feeds/connectors) to other docker registries. These connectors work a little differently than other connectors in ProGet. See [Docker connectors](connectors) for details.

## Private Registry Limitations {#limitations data-title="Private Registry Limitations"}

### Windows Integrated Authentication

The Docker client does not support Windows Integrated Authentication, which means that you won't be able to setup "Anonymous" access to a ProGet instance that has this enabled. To workaround this, you can set-up a second site in IIS without Windows Integrated Authentication enabled that points to the same path on disk.

### Other Limitations

Docker is designed to tightly integrate with the publicly-hosted hub.docker.com. Private registries are supported to some extent, but the Docker client and related tooling always assume you will be using their public registry, or at the very least, the official private [Docker Registry](https://docs.docker.com/registry) that they built and support. This can make working with private registries a bit awkward at times, but Docker client support is gradually improving.
