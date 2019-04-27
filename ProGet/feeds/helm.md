---
title: Helm (Kubernetes)
subtitle: Helm Feeds in ProGet
sequence: 500
keywords: proget,feeds,helm,kubernetes
show-headings-in-nav: true
---

[Helm](https://helm.sh/) is the package manager for [Kubernetes](https://kubernetes.io/) applications. A ProGet package for a Helm feed is known as a [chart](https://helm.sh/docs/developing_charts/#charts), which can be used to describe even the most complex application, and provide a repeatable, versioned approach to Kubernetes deployments.

This feed type is available starting in ProGet 5.2.{.info}

## Pre-requisite Configuration

In order to install packages from a ProGet feed, it must be added to the local list of repositories by running this command:

```
helm repo add proget http://{proget-server}/helm/{feed-name}
```

{.attention .best-practice} Using `proget` is recommened for the repo name, unless there are multiple feeds configuration in ProGet, in which case `proget-{feed-name}` should be used.

## Common Tasks

### Installing Helm Charts

To install a chart hosted by ProGet, run the following the commands: 

```
helm repo update
helm install proget/{chart-name}
```

{.attention .technical} Note: The helm cli references `--repo` as the argument to install from a custom repository, but that will fail, likely with the following error message: "`Error: Could not find protocol handler for:`"

### Creating Helm Charts

A basic Helm chart can be created using the following commands:

```
helm create {chart-name}
helm package {chart-name}
```

The `create` command will create a template/skeleton chart which can be modified as desired. The `package` command will package the chart into a `.tgz` file that can be pushed to ProGet. 

Refer to the [Helm chart creation documentation](https://helm.sh/docs/using_helm/#creating-your-own-charts) for more information.

### Publishing Charts

The `helm` executable does not support pushing Helm charts, so ProGet offers 3 alternative methods to add charts to ProGet Helm feed:

#### Upload from ProGet Web Application

On the feed overview page, select "Add Package" and "Upload Chart" to upload a packaged Helm chart (i.e. `.tgz` file generated from the `helm package` command.

#### Publish via HTTP

To push a Helm chart via HTTP, issue a PUT or POST request with the package file as the content to: `http://{proget-server}/helm/{feed-name}`

This can be accomplished with the following examples: 

##### PowerShell

```
Invoke-WebRequest http://{proget-server}/helm/{feed-name} -Headers @{"AUTHORIZATION"="Basic " + [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("{username}:{password}"))} -Method PUT -InFile {chart-name}-{chart-version}.tgz
```

##### cURL

```
curl http://{proget-server}/helm/{feed-name} --user <user>:<password> --upload-file <chart-name>-<chart-version>.tgz
```

#### Pull From External Repository

If the chart version you would like to install is available in an external Helm repository, use this option.

### Technical Limitations

 - Signed (i.e. provenance) charts are not supported at this time, but may become available in a future v5.2 maintenance release
