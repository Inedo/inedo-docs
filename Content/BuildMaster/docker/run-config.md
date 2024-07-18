---
title: "Deployment With Docker Run Config"
order: 3
---

Like most software, containerized applications should use a repeatable release process (i.e., a [pipeline](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines)) that can deploy containers across multiple testing and production environments.

BuildMaster's Docker Run Config and Docker Deployment Operations make it easy to tag and push container images, as well as stop  and start containers using sensitive, environment-specific configuration.


## What is a "Docker Run Config"?
*Docker Run Config is a BuildMaster 2023 Feature, and available on a preview basis in BuildMaster 2022.13 and later.*

A "Docker Run Config" is a special type of [application configuration file](/docs/buildmaster/deployment-continuous-delivery/buildmaster-applications-configuration-files) that's designed to work with BuildMaster's `Docker::Run` operation.

![BuildMaster-DockerRunConfig](/resources/docs/BuildMaster-DockerRunConfig.png){height="" width="50%"}

Using Docker Run Config is not required, and you can use [BuildMaster's Docker Deployment Operations](#buildmaster-deployment-operations) for more advanced scenarios.

### Configuration Instances & Environments
A Docker Run Config file has multiple "instances" with different configurations based on the environment you'll deploy to. You can control who has access to view or edit these instances based on environment.

![BuildMaster-DockerRunConfig-EditInstance](/resources/docs/BuildMaster-DockerRunConfig-EditInstance.png){height="" width="50%"}

Each instance will show up as "tab" when you edit a Docker Run Config, and each tab will contain multiple lines that are used as [OPTIONS for Docker's "run" command](https://docs.docker.com/engine/reference/run/). These lines can be edited visually or in text mode, and are formatted identically to the command options.

This approach is suitable for deploying most single-container applications. If you're deploying multi-container applications using Docker Compose, you can use `Docker::Compose` operation and a configuration file to store the YAML in a similar manner.

## Creating a Docker Run Configuration
To create a Docker Run Config, click on "Docker Run Configs" under the Docker menu of your application, and then click "Create Docker Run Config" in the upper right hand corner. If you don't see these options, you may need to enable Docker under Build/Release features in the Settings menu of your application.

![BuildMaster-Create-DockerRunConfig](/resources/docs/BuildMaster-Create-DockerRunConfig.png){height="" width="50%"}

When creating a Docker Run Config file, you'll be guided to add a Volume Mapping, Port Mapping, Environment Variable, and Resource Usage. You can skip these steps or add/edit additional values later.

### Docker Run Config Options
You can add the following options to a Docker Run Config.

* **Volume Mappings** will map a directory in your container to one stored on the Docker server using `-v «container-path»:«server-path»`
* **Label** will apply an [object label](https://docs.docker.com/config/labels-custom-metadata/) to the container using `-l «key»:«value»`
* **Environment Variable** will set an environment variable inside the container using the `-e «key»:«value»`
* **Port Mapping** will map a port in your container to one on the Docker server using `-p «container-port»:«server-port»`
* **Resource Usage** will restrict CPU Cores, Memory, and GPUs usage for the container using `--cpus=«value»`, `--memory=«value»`, and `--gpus «value»` 
    
Each of these will appear as a line in the file, and you can edit them in visual or text modes. For example, here's what the text mode for an instance may look like.

```
-e SqlConnection="Data Source=MyDatabase; Initial Catalog=$ApplicationName; User ID=sa; Password=Password!"
-e ApiKey=myInsecurePassw0rd!
-v /content:/home/myproject/content
-p 80:8080
-p 443:8083
```

### Specifying Additional Run Options
If you need additional options, they can specified using the "Additional Run Arguments" parameter on the `Docker::Run ` operation. Let us know if this is the case - we'd be happy to add support for them a  Docker Run Config. 

## Using Docker Run Config to Deploy
The easiest way to deploy a container image is with the "Deploy Docker Image" Script Template. 

![buildmaster-docker-deploy-script-template](/resources/docs/buildmaster-docker-deploy-script-template.png){height="" width="50%"}

This script template uses a Docker Run Config file to deploy the Docker image associated with the current build. This association automatically happens at build time, and the script uses the values of `$DockerRepository` and `$DockerTag`. 

The deployment logic is as follows:

1. Use `docker stop` to terminate the previously-deployed container
2. Use `docker pull` to retrieve the appropriate container image from the repository
3. Use `docker run` to start the just-pulled container image

The "Container name" (on the "Docker Options" tab) is set when running the image, which is why the stop command will terminate the previously-deployed container.

Like all [Deployment Scripts Templates](/docs/buildmaster/deployment-continuous-delivery/buildmaster-deployment-scripts), you can convert to OtterScript for more advanced properties and scenarios.

## BuildMaster Deployment Operations
BuildMaster Docker operations are designed to work with a [Docker Repository](/docs/buildmaster/docker) that you've connected to your application. This repository will be connected to credentials that can be used to authenticate (`docker login`) to the registry where the image is hosted.

:::(Info) (💡 Working with Multiple Docker Repositories)
In general, we recommend having one Docker Repository per application in BuildMaster, and one image per build. But you can connect as many repositories to your application as needed, and build as many images as you'd like. 

However, because a build can only be associated with one repository, you won't be able to rely on values of `$DockerRepository` and `$DockerTag` in your build and deployment scripts.
:::

All of the Docker operations allow you to override the `DockerExePath` and force whether "Docker on WSL" should be used (`UseWsl`).

### Docker::Run
`Docker::Run` is the primary operation used to deploy images; it will pull the target image then run the container on the server.

The following logic is used.

1. `docker login` to the registry where the repository is hosted
2. `docker pull` the target docker image 
3. `docker run` the target image, optionally using a Docker Run Config
5. `docker logout`

You can set the following parameters on the operation:

<table>
    <tr><th>Category</th><th>Parameter</th><th>Notes</th></tr>
    <tr><td rowspan="2">General</td><td>Repository</td><td>name of the Docker Repository connected to the application; defaults to <code>$DockerRepository</code></td></tr>
    <tr><td>Tag</td><td>Tag to pull from the repository, defaults to <code>$DockerTag</code></td></tr>
<tr><td rowspan="2">Run Config</td><td>DockerRunConfig<td>optional; the Docker Run Config file to use</td></tr>
    <tr><td>DockerRunConfigInstance</td><td>Instance of the Docker Run Config to use, defaults to $PipelineStageName</td></tr>
    <tr><td rowspan="5">Advanced</td><td>Restart</td><td>the [restart policy](https://docs.docker.com/config/containers/start-containers-automatically/) to specify with the `--restart` flag; defaults to <code>unless-stopped</code></td></tr>
    <tr><td>ContainerName</td><td>Name to use for the running container; defaults to a name based on the repository name</td></tr>
    <tr><td>RunInBackground</td><td>Run the image detached (`--d`), defaults to true</td></tr>
    <tr><td>RemoveOnExit</td><td>Remove the container once it stops (`--rm`), defaults to false</td></tr>
    <tr><td>AdditionalArguments</td><td>Additional parameters to pass to `docker run`</td></tr>
</table>

### Docker::Stop
The `Docker::Stop` will stop a running container using `docker stop` and then optionally remove it using `docker rm`.

<table>
    <tr><th>Category</th><th>Parameter</th><th>Notes</th></tr>
    <tr><td rowspan="3">General</td><td>ContainerName</td><td>name of the running container; defaults to a name based on <code>$DockerRepository</code></td></tr>
    <tr><td>Remove</td><td>Remove the container when stopped, defaults to true</td></tr>
    <tr><td>FailIfContainerDoesNotExist</td><td>raises an error if the target container does not exist; defaults to false</td></tr>
</table>

### Docker::Tag Operation
The `Docker::Tag` operation will add a tag to an image in a repository, and should generally be used to indicate that an image is production-ready. For example, you might retag`myapp:4.2.1-pre.14` to `myapp:4.2.1` once it's deployed to production.

You can also use `Docker::Tag` to push an image to a new repository. This may be a common practice if you use different prerelease and production registries.

The following logic is used.

1. `docker login` to the registry where the repository is hosted
2. `docker pull` the target docker image 
3. `docker tag` the image using the new tag
4. `docker push` the image to the repository indicated by the tag
5. `docker logout`
6. Attach the new image/tag to the build in BuildMaster

You can set the following parameters on the operation:

<table>
    <tr><th>Category</th><th>Parameter</th><th>Notes</th></tr>
    <tr><td rowspan="2">Source</td><td>Repository</td><td>name of the Docker Repository connected to the application; defaults to <code>$DockerRepository</code></td></tr>
    <tr><td>OriginalTag</td><td>Tag to pull from the repository, defaults to <code>$DockerTag</code></td></tr>
<tr><td rowspan="2">Destination</td><td>NewRepository<td>optional; a Docker repository to push the image to</td></tr>
    <tr><td>NewTag</td><td>New tag to push to the repository</td></tr>
    <tr><td rowspan="2">Advanced</td><td>AttachToBuild</td><td>Attach the tagged image to the build, which will cause it to be displayed in UI (defaults to true)</td></tr>
    <tr><td>DeactivateOriginalTag</td><td>Detach the original tag from the build, defaults to true</td></tr>
</table>


### Docker::Exec
The `Docker::Exec` operation will run a command against a running container using `docker exec`. This is often used during deployment to configure services after start-up.

<table>
    <tr><td>ContainerName</td><td>Name to use for the running container; defaults to a name based on $RepositoryName</td></tr>
    <tr><td>Command</td><td>the command to run inside the running container</td></tr>
    <tr><td>WorkDir</td><td>the folder inside the running container to execute the command from (`--workdir)</td></tr>    
    <tr><td>RunInBackground</td><td>Run the command detached (`--d`), defaults to true</td></tr>
    <tr><td>Interactive</td><td>Keep the standard input stream open (`--interactive`); this will effectively log output </td></tr>
    <tr><td>AdditionalArguments</td><td>Additional parameters to pass to `docker run`</td></tr>
</table>

