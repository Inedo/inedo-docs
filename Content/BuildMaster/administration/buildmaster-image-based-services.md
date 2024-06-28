---
title: "Image-based Services (Containerized Builds)"
order: 4
---

BuildMaster's Image-based Services allow you to use containers images like `microsoft-dotnet-sdk` and `maven` that have the tools and components required to build your application (such as the .NET SDK, Java SDK, etc.) already installed. 

This feature is *not* intended for containerizing your application (i.e. building Docker images), but for building your applications *using* a container. See [Containerizing your Application](/docs/buildmaster/docker/containerizing-applications) to learn how to create container images in BuildMaster.

Image-based Services are a BuildMaster 2023 Feature, and available on a preview basis in BuildMaster 2022.13 and later.

## Enabling and Customizing Image-based Services
To enable Image-based Services, navigate to Admin > Image-based Servers.

:::(Warning) (Docker Engine is Required)
Before enabling this feature, a Docker engine must be installed on either the BuildMaster server, or on [a server connected to BuildMaster](/docs/buildmaster/administration-agents-and-infrastructure/buildmaster-servers). If BuildMaster cannot detect a Docker engine, you won't be able to enable or use image-based services.

See [Installing & Integrating with Docker Engine](/docs/buildmaster/docker/engine-integration) to learn more.
:::

After enabling the feature, you'll see a list of Registered Image-based Services. You can edit or add to this list as needed.

![buildmaster-ibs-list](/resources/docs/buildmaster-ibs-list.png){height="" width="50%"}

### Customizing Image-based Services

An Image-based Service  is essentially a reference to a Docker image in a repository, and is comprised of the following fields:

* **Name**; a user-friendly name that you'll use to reference the service
* **Repository name**; a repository that the Docker client will understand, such as `progetsvr.corp/dev-images/corp/dotnetbuild`
* **Capabilities**; used to filter lists in the User Interface, the compatible platforms that the container can build
* **Tag**; a specific tag within the repository, with `*` supported as an alias
* **Credential**; for registries that require authentication, the account to authenticate with

BuildMaster includes references to the official container images for a variety of platforms; you can edit these or add your own.

## Using Containers to Build 
When [Image-based Services](/docs/buildmaster/administration/buildmaster-image-based-services) are enabled, script templates like [Build .NET Project](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts/buildmaster-build-net-project-script-template) and [Build Maven Project](/docs/buildmaster/builds-continuous-integration/buildmaster-build-scripts/buildmaster-build-maven-project-script-template) will display a "Docker Options" tab that lets you select the Image-based Service to run your build script in.

The list of available services will depend on the capabilities; for example, the "Build .NET Project" template will show `.NET`-capable services.

### Implementation Details
Image-based Services use `docker run` and always pull the image for the tag registered in BuildMaster. The container is then automatically deleted after the run completes.

:::(Info)
Only the underlying build tool (such as `dotnet`, `maven`, etc.) will be run in the container.  
:::

Prior to running, BuildMaster will mount the working directory and automatically capture build output.

Operations which support Image Based Services:

* `DotNet::Build`
* `DotNet::Publish`
* `Java::Maven`
* `npm::Build`
