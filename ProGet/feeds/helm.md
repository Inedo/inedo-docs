---
title: Helm (Kubernetes)
subtitle: Helm Feeds in ProGet
sequence: 500
keywords: proget,feeds,helm,kubernetes
show-headings-in-nav: true
---

[Helm](https://helm.sh/) is the package manager for [Kubernetes](https://kubernetes.io/) applications. A ProGet package for a Helm feed is known as a [chart](https://helm.sh/docs/developing_charts/#charts), which can be used to describe even the most complex application and provide a repeatable, versioned approach to Kubernetes deployments.

This feed type is available starting in ProGet 5.2.{.info}

## Pre-requisite Configuration {#prereq data-title="Pre-requisite"}

In order to install packages from a ProGet feed using helm, the ProGet feed must be added to the local list of repositories by running this command:

```
helm repo add proget http://{proget-server}/helm/{feed-name}
```

{.attention .best-practice} Using `proget` is recommended for the repo name, unless there are multiple feeds configured in ProGet, in which case `proget-{feed-name}` should be used.

## Common Tasks {#commontasks data-title="Common Tasks"}

### Installing Helm Charts

To install a chart hosted by ProGet, run the following the commands: 

```
helm repo update
helm install proget/{chart-name}
```

{.attention .technical} Note: The Helm CLI references `--repo` as the argument to install from a custom repository.  ProGet is not compatible with the `--repo` argument, you will likely receive the following error message: "`Error: Could not find protocol handler for:`". ProGet will only work by adding a repo using the `helm repo add` command.

### Creating Helm Charts

A basic Helm chart can be created using the following commands:

```
helm create {chart-name}
helm package {chart-name}
```

The `create` command will create a template/skeleton chart which can be modified as desired. The `package` command will package the chart into a `.tgz` file that can be pushed to ProGet. 

Refer to the [Helm chart creation documentation](https://helm.sh/docs/using_helm/#creating-your-own-charts) for more information.

### Publishing Charts

The `helm` executable does not support pushing Helm charts, so ProGet offers three alternative methods to add charts to ProGet Helm feed:

#### Upload from ProGet Web Application

On the feed overview page, select "Add Package" and "Upload Chart" to upload a packaged Helm chart (i.e., `.tgz` file generated from the `helm package` command).

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

Signed (i.e., provenance) charts are not supported at this time but may become available in a future v5.2 maintenance release.

## Container Visibility {#containers data-title="Container Visibility"}

Helm charts reference a number of container images. These container images are defined in a `values.yaml` file that is used to populated the `deployments.yaml` template file. A chart may also be dependent on a number of other helm charts. These dependencies also have a `values.yaml` file and `deploment.yaml` template.

ProGet will parse the `values.yaml` of the chart and its dependencies `values.yaml` files to find which container images are used. These container images will then be displayed in the chart's description under the `Associated Container Images` section. ProGet will also attempt to match these associated container images with a container image within your ProGet container registries. If a match is found, ProGet will automatically link the Helm chart to associated ProGet container image.

_*This feature is available in ProGet 5.3 and above._

### Parsing Requirements

When configuring container images in your `values.yaml`, the image property must end in _image_ (e.g., _image:_, _redisImage:_, _postgresImage:_, etc.) and contain the following sub-properties: _repository_, _tag_ (or _dockerTag_), and optionally _registry_. If _tag_ or _dockerTag_ is omitted, the `latest` tag will be used when defining the associated container image.

### Container Vulnerabilities {#vuln data-title="Container Vulnerabilities"}

If you have configured your container registries to leverage a [vulnerability source](/docs/proget/compliance/vulnerabilities), you may have a Helm chart that links to a vulnerable container image. This may cause your Kubernetes install to fail when attempting to pull the container image from your registry. Container images will be blocked if a vulnerability has been assessed and marked `Blocked` or if you have enabled the `Unassessed` vulnerabilities to be blocked in _Administration > Advanced Settings_.
