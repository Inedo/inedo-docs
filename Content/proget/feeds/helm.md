---
title: "Helm (Kubernetes)"
order: 12
---

[Helm](https://helm.sh/) is the package manager for [Kubernetes](https://kubernetes.io/) applications. A ProGet package for a Helm feed is known as a [chart](https://helm.sh/docs/developing_charts/#charts), which can be used to describe even the most complex application and provide a repeatable, versioned approach to Kubernetes deployments.

This feed type is available starting in ProGet 5.2.

## Pre-requisite Configuration 

In order to install packages from a ProGet feed using Helm, the ProGet feed must be added to the local list of repositories by running this command:

```bash
helm repo add proget http://{proget-server}/helm/{feed-name}
```

:::(Info) 
Using `proget` is recommended for the repo name, unless there are multiple feeds configured in ProGet, in which case `proget-{feed-name}` should be used.
:::

## Common Tasks 

### Installing Helm Charts

To install a chart hosted by ProGet, run the following commands: 

```bash
helm repo update
helm install proget/{chart-name}
```

:::(Error)
 Note: The Helm CLI references `--repo` as the argument to install from a custom repository.  ProGet is not compatible with the `--repo` argument, you will likely receive the following error message: "`Error: Could not find protocol handler for:`". ProGet will only work by adding a repo using the `helm repo add` command.
:::

### Creating Helm Charts

A basic Helm chart can be created using the following commands:

```bash
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

To push a Helm chart via HTTP, issue a `PUT` or `POST` request with the package file as the content to: `http://{proget-server}/helm/{feed-name}`

This can be accomplished with the following examples: 

#### PowerShell

```powershell
Invoke-WebRequest http://{proget-server}/helm/{feed-name} -Headers @{"AUTHORIZATION"="Basic " + [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("{username}:{password}"))} -Method PUT -InFile {chart-name}-{chart-version}.tgz
```

#### cURL

```bash
curl http://{proget-server}/helm/{feed-name} --user <user>:<password> --upload-file <chart-name>-<chart-version>.tgz
```

### Deleting (Unpublishing) Charts

You can delete (permanently remove) Helm charts from your feed by navigating to the package page and clicking the Delete Package button. This action requires the `Feeds_DeletePackage` permission attribute.

To programmatically delete a Helm chart from your feed, issue a `DELETE` request to: `http://{proget-server}/helm/{feed-name}/package/{ID}/{VERSION}`

This can be accomplished with the following examples: 

#### PowerShell

```powershell
Invoke-WebRequest http://{proget-server}/helm/{feed-name}/package/{ID}/{VERSION} -Headers @{"AUTHORIZATION"="Basic " + [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("{username}:{password}"))} -Method DELETE
```

#### cURL

```bash
curl http://{proget-server}/helm/{feed-name}/package/{ID}/{VERSION} --user <user>:<password> -X DELETE
```

#### What about the  `helm-push` command?

The `helm-push` command comes from a third-party plugin that is designed exclusively to push packages to ChartMusuem (which is a private Helm repository). It is not a "standard" and is only compatible with the ChartMusuem API, and behind the scenes, it seems quite complicated. With the other methods, you just upload the file you specify and get a standard HTTP status code in response.

The reason it doesn't work with ProGet is that the ChartMusuem repository is a very different product than ProGet; for example, it doesn't have the concept of "Feeds", or even custom URL endpoint points. This means that only a root path like `https://proget.kramerica.corp/` is supported. Even if we could "hack something together" by reverse-engineering and re-implementing the ChartMusuem API, it could unexpectedly break as they release new versions of the plug-in and clients.

#### Pull From External Repository

If the chart version you want to install is available in an external Helm repository, use this option.

### Technical Limitations

Signed (i.e., provenance) charts are not supported at this time but may become available in a future v5.2 maintenance release.

## Container Visibility 

Helm charts reference a number of container images. These container images are defined in a `values.yaml` file that is used to populate the `deployments.yaml` template file. A chart may also be dependent on a number of other Helm charts. These dependencies also have a `values.yaml` file and `deploment.yaml` template.

ProGet will parse the `values.yaml` of the chart and its dependencies `values.yaml` files to find which container images are used. These container images will then be displayed in the chart's description under the `Associated Container Images` section. ProGet will also attempt to match these associated container images with a container image within your ProGet container registries. If a match is found, ProGet will automatically link the Helm chart to the associated ProGet container image.

**This feature is available in ProGet 5.3 and above.*

### Parsing Requirements

When configuring container images in your `values.yaml`, the image property must end in _image_ (e.g., _image:_, _redisImage:_, _postgresImage:_, etc.) and contain the following sub-properties: _repository_, _tag_ (or _dockerTag_), and optionally _registry_. If _tag_ or _dockerTag_ is omitted, the `latest` tag will be used when defining the associated container image.

### Container Vulnerabilities

If you have configured your container registries to leverage a [vulnerability source](/docs/proget/sca/vulnerabilities), you may have a Helm chart that links to a vulnerable container image. This can cause your Kubernetes installation to fail when you try to pull the container image from your registry. Container images will be blocked if a vulnerability has been assessed and marked `Blocked` or if you have enabled the `Unassessed` vulnerabilities to be blocked in "Administration Overview" > "Advanced Settings".