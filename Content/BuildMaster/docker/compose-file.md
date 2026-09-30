---
title: "Deployment With Docker Compose"
order: 4
---

Like most software, containerized applications should use a repeatable release process (i.e., a [pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines)) that can deploy containers across multiple testing and production environments.

BuildMaster's Docker Operations, Docker Compose Files, and Docker Compose Deployment Operations make it easy to tag and push container images, as well as stop and start containers using sensitive, environment-specific configuration.


## What is a "Compose File"?
*Docker Compose Files are a BuildMaster 2026 Feature.*

A "Docker Compose File" is a special type of [application configuration file](/docs/buildmaster/deployment-continuous-delivery/buildmaster-applications-configuration-files) that's designed to work with BuildMaster's `Docker::Compose-DeployComposeFile` operation.

![BuildMaster-DockerComposeFile](/resources/docs/buildmaster-edit-docker-compose-file.png){height="" width=""}

Using Docker Compose Files are not required, and you can use [BuildMaster's Docker Compose Operations](#buildmaster-deployment-operations) for more advanced scenarios.

### Configuration Instances & Environments
A Docker Compose File has multiple "instances" with different configurations based on the environment you'll deploy to. You can control who has access to view or edit these instances based on environment.

![BuildMaster-DockerComposeFile-EditInstance](/resources/docs/BuildMaster-DockerComposeFile-EditInstance.png){height="" width="50%"}

Each instance will show up as "tab" when you edit a Docker Compose File, and each tab will contain a separate Docker Compose YAML.

This approach is best used when you have a more advanced deployment options than what the [Docker Run Config](/docs/buildmaster/docker/run-config) offers or if you have multiple images to deploy.

## Creating a Docker Compose File
To create a Docker Compose File, click on "Docker Compose Files" under the Docker menu of your application, and then click "Create Docker Compose File" in the upper right hand corner. If you don't see these options, you may need to enable Docker under Build/Release features in the Settings menu of your application.

![BuildMaster-Create-BuildMaster-CreateDockerComposeFile](/resources/docs/BuildMaster-Create-DockerComposeFile.png){height="" width="50%"}

## Using Docker Compose Files to Deploy
The easiest way to deploy a container image using Docker Compose is with the "Deploy via Docker Compose" Script Template. 

![buildmaster-docker-deploy-script-template](/resources/docs/buildmaster-docker-compose-deploy-script-template.png){height="" width="50%"}

This script template uses a Docker Compose File to deploy the Docker image associated with the current build. This association automatically happens at build time, and the script uses the values of `$DockerRepository` and `$DockerTag`. 

The deployment logic is as follows:

1. Deploy the Docker Compose File
2. Use `docker up -d` to pull and start the containers

Like all [Deployment Scripts Templates](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts), you can convert to OtterScript for more advanced properties and scenarios.

## BuildMaster Deployment Operations
BuildMaster Docker Compose operations are designed to work with a [Docker Repository](/docs/buildmaster/docker) that you've connected to your application. This repository can be connected to credentials that can be used to authenticate using `Docker::Login` (`docker login`) to the registry where the image is hosted.

:::(Info) (💡 Working with Multiple Docker Repositories)
In general, we recommend having one Docker Repository per application in BuildMaster, and one image per build. But you can connect as many repositories to your application as needed, and build as many images as you'd like. 

However, because a build can only be associated with one repository, you won't be able to rely on values of `$DockerRepository` and `$DockerTag` in your Docker Compose Files, build scripts, and deployment scripts.
:::

All of the Docker operations allow you to override the `DockerExePath` and force whether "Docker on WSL" should be used (`UseWsl`).

### Operations

- `Docker::Compose-DeployComposeFile` deploys a Docker Compose file stored in BuildMaster.
- `Docker::Compose-Up` runs `docker compose up -d` on the specified compose file.
- `Docker::Compose-Down` runs `docker compose down` on the specified compose file.
- `Docker::Compose-Exec` runs commands on currently running containers that were started using Docker::Compose-Up.
- `Cocker::Compose-Run` starts a new container for the specified service in the compose file and runs a command.

::: (Info) (Note:)
Operation parameters may vary based on your version of BuildMaster.  For the operation details for your version of BuildMaster, see the parameter specifications in Administrations -> Operations.
:::





