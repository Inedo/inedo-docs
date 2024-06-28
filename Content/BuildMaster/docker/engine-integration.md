---
title: "Installing & Integrating with Docker Engine"
order: 1
---

BuildMaster's [Docker/Containers features](/docs/buildmaster/docker) rely on a [Docker Engine](https://docs.docker.com/engine/) that's installed on a Linux or Windows server. 

This article provides guidance on how to install Docker Engine on a server, as well as how BuildMaster detects and integrates with Docker.

## How to Install Docker Engine
Although Linux is the most popular operating system for Docker Engine, you can also install it on Windows. In either case, it's really easy: just run one of the official installation scripts that we've linked below.

Aside from resource limitations, there's no problem installing or using a Docker Engine on the same server as BuildMaster. If you're running BuildMaster on Linux, then can simply connect to the host's server using an SSH-based agent.

### Installing Docker Engine on Linux
Installing Docker on Linux is relatively easy, especially if you use [Docker's convenience script](https://get.docker.com/). You simply run this command to automatically install Docker:

```sh
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```
Otherwise, just follow [Docker's official installation documentation](https://docs.docker.com/engine/install/) for the commands you'll need to run.
                            
### Installing Docker Engine on Windows
Docker on Windows comes in two flavors, depending on the types of containers you want to work with:
* **Docker for Windows** allows you build and run Windows-based containers on a server, but you won't be able to build or run Linux-based containers.
* **Docker on WSL** allows you to build and run Linux-based containers on a server, but it's more complex and Microsoft does not support it in production environments.

If you plan to work with Linux-based containers, we recommend using a Linux-based server instead.

#### How to install Docker for Windows
To build or run Windows containers on a Windows server, you'll need to enable certain Windows features and install a supported container runtime.  

:::(Warning) (Docker Desktop is Not Supported)
[Docker Desktop](https://www.docker.com/products/docker-desktop/) is only compatible with Windows desktop systems (e.g. Windows 11) and cannot be installed on Windows Server. We do not recommend installing BuildMaster on a desktop system.
:::

The easiest way to install Docker for Windows is by running [Microsoft's quick installation script](https://raw.githubusercontent.com/microsoft/Windows-Containers/Main/helpful_tools/Install-DockerCE/install-docker-ce.ps1) to install Moby, which is Microsoft's recommended runtime.

```powershell
Invoke-WebRequest`
  -UseBasicParsing`
  'https://raw.githubusercontent.com' +`
    '/microsoft/Windows-Containers/Main/helpful_tools' +`
    '/Install-DockerCE/install-docker-ce.ps1'`
  -o install-docker-ce.ps1

.\install-docker-ce.ps1
```

However, there are other supported runtimes such as Mirantis and Containerd. See [Microsoft’s documentation](https://learn.microsoft.com/en-us/virtualization/windowscontainers/quick-start/set-up-environment#windows-server-1) to learn more.

#### How to install Docker using WSL
To build or run Linux containers on a Windows server, you'll need to enable certain Windows features, install WSL2, install Ubuntu, and then finally install Docker. 

:::(Warning)
According to Microsoft, WSL was designed for "inner loop development workflows" and may be "challenging for production-related scenarios." We're not sure if that means WSL is not suitable for production, but you can read [Can I use WSL for production scenarios?](https://learn.microsoft.com/en-us/windows/wsl/faq#can-i-use-wsl-for-production-scenarios--) and decide for yourself.
:::

Installing Docker on WSL is bit complex and it may not work on your hardware. 

We plan to write our own tutorial for Installing Docker on WSL, but in the meantime we recommend following the steps in this blog: [How To Install Docker Without Docker Desktop On Windows](https://www.paulsblog.dev/how-to-install-docker-without-docker-desktop-on-windows/)

:::(Info) (Note:)
Note that Docker Compose and Portainer are not required for building or running containers using BuildMaster.
:::

## How BuildMaster Detects Docker Engine
BuildMaster will automatically detect when Docker Engine is installed on the BuildMaster server or on any connected server. This detection occurs during routine server scanning, and can be manually triggered on the Servers page with the Refresh Status button.

To determine which Docker Engine is installed on a Linux-based server, BuildMaster will attempt to execute `docker version`. On a Windows-based server, BuildMaster will run `docker.exe version` (to check for Docker for Windows) and `wsl.exe docker version` (to check for Docker on WSL). If these result in an error (such as file not found), then Docker Engine is assumed to not be present. 

When Docker is detected on a server, BuildMaster will routinely check for containers using `docker ps`. You can view these containers by clicking the "view containers" link on the server's details page.