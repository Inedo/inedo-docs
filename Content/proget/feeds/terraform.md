---
title: "Terraform Modules"
order: 20
---

[Terraform Modules](https://developer.hashicorp.com/terraform/language/modules) are used to package and reuse resource configurations with Terraform.

A Terraform feed in ProGet acts as a private module registry that allows you to store your own modules. You can also create connectors to other Terraform Module Registries such as the [Hashicorp registry](https://registry.terraform.io/) to use third-party modules through a Terraform feed and create a curated list of approved Terraform modules.

Terraform feeds are available in ProGet ProGet 2024.20+.

## Using Terraform Feeds as a Private Registry

Before using a Terraform Feed as a Private Module Registry, you'll need to package your modules. This is as simple as zipping the content and uploading it to the ProGet UI or using the `upack` CLI; see [Terraform Module Packages](#terraform-module-packages) to learn more.

Once you've uploaded a package to your feed, you can add the module to your Terraform configuration using the following format:

```
module "«module-name»" {
  source  = "«proget-host-name»/«feed-name»__«namespace»/«module-name»/«provider»"
  version = "«version»"
}
```

For example the `my-company/my-module` module that uses `aws` might look like this:

```
module "example_module" {
  source = "proget.corp/internal-terraform__my-company/my-module/aws"
  version = "4.20.0"
}
```

Once you add the module to your configuration, you can add in additional variables and then run `terraform init`.

## Connecting to another Terraform Module Registry

You can connect a Terraform feed to another Terraform Registry, such as `registry.terraform.io`. Modules that are downloaded or pulled through the feed will be automatically converted into a package and cached/installed in the feed. See [Terraform Module Packages](#terraform-module-packages) to learn more.

### Connector Listing & Search Limitations

Unless the remote registry implements a special search/listing APIs like `registry.terraform.io`, you will not see a listing of modules. However, you can find the modules if you type in their 3-part name: `«namespace»/«module-name»/«provider»`. ProGet feeds do not currently implement this search API, which means self-connectors will not list packages from another feed.

### Connector Download Limitations

Because a Terraform Module Registry simply provides the Terraform CLI with a "pointer" to download content, ProGet needs to decode and follow those pointers to download and package the module's content.

ProGet supports two types of download pointers:

* [HTTP Urls to .zip files](https://developer.hashicorp.com/terraform/language/modules/sources#http-urls), which most private repositories appear to use
* [Generic Git Repositories at GitHub.com](https://developer.hashicorp.com/terraform/language/modules/sources#generic-git-repository), which the Hashicorp registry seems to exclusively use

Unlike the Terraform CLI, ProGet does not use the Git client to download the module files from GitHub. Instead, ProGet uses a rudimentary string replacement to download the repository from  GitHub's archive endpoint. 

For example, the Hashicorp registry responds with this pointer when requesting a module:
```
X-Terraform-Get: git::https://github.com/sourcegraph/terraform-aws-executors?ref=d9d6b1db18013b7dbd01edce457980c58e7a1c90
```

ProGet will transform this pointer to a usable GitHub URL:
```
https://github.com/sourcegraph/terraform-aws-executors/archive/d9d6b1db18013b7dbd01edce457980c58e7a1c90.zip
```

The contents will then be downloaded from that URL and packaged. If you run into any issues with specific modules from the Hashicorp registry or another one, [please let us know](https://forums.inedo.com).

## Authenticated Feeds

If your feed is not configured for anonymous access, then you'll need to configure the Terraform CLI to authenticate.

This can be done be either setting an environment variable with your API Key (`TF_TOKEN_{hostName}=«api-key»`) or by adding  the following to your CLI Configuration File (`.terraformrc` or `terraform.rc`):
                
```
credentials "«proget-hostname»" {
   token = "«api-key»"
}
```

Note that the Terraform CLI limits authentication by host name, which means that only one API Key per host name is possible. See the [Terraform's CLI Configuration documentation](https://developer.hashicorp.com/terraform/cli/config/config-file#credentials-1) and/or [Creating and Managing API Keys in ProGet](/docs/proget/reference-api/proget-apikeys#creating-and-managing-api-keys) to learn more.


## Terraform Module Packages

Terraform Modules are not distributed using [self-describing package files](/docs/proget/packages/what-is-a-package); instead, a Terraform module registry (e.g. `registry.terraform.io`) is simply a list of versions and a "pointer" to a GitHub repository where the files can be downloaded. 

While this makes things simpler for publishers to the free and open-source Hashicorp registry, it's not a good fit for organizations that rely on reliable and repeatable processes. For example, the GitHub repository could be deleted or changed, meaning the module will suddenly no longer be available.

Terraform Feeds in ProGet store modules in self-describing [Universal Packages](/docs/proget/upack/upack-universal-packages). This not only makes the modules portable and independent from external resources, but cryptographically seals them.

### Creating Terraform Module Packages

A Terraform Module Package is a specially-formatted ZIP file:

1. the file has a .upack file extension (not .zip)
2. there is a manifest file in the root directory named `upack.json` with the following properties
   a. `group` is used for the module's namespace
   b. `name` is formatted with 2-parts (`«module-name».«provider»`)
   c. `version` is a valid, [3-part semantic version number](https://semver.org/)
3. The `/package` directory in the zip file contains the module's content


You can create a Terraform Module Packages from the ProGet UI or using the `upack` [commandline client](/docs/proget/upack/upack-tools-and-libraries/upack-tools-and-libraries-upack-cli).

#### Example Package Manifest
For example, a `upack.json` for the `my-company/my-module/aws` module might look like this:

```
{
  "group": "my-company",
  "name": "my-module.aws",
  "version": "4.20.0"
}
```

You can also use additional Universal Package properties like `summary` and `description` to provide additional metadata for users in ProGet.

### Automatically-Created Module Packages

When a module is downloaded or pulled into a Terraform Feed, a universal package will be automatically created from the contents. The `upack.json` manifest files in these packages will contain some additional metadata.

```
"modulePackaging": {
   "packagedDate": "2024-11-06T22:37:41.2253568Z",
   "using": "ProGet/24.0.0.0",
   "registry": "https://registry.terraform.io/",
   "registryMetadata": {
      "id": "terraform-aws-modules/vpc/aws/3.14.1",
      ...
   }
}
```

This data is only provided for auditing purposes and as a "snapshot" of what was used to create the package.

## Support for Terraform Providers

ProGet's Terraform Feeds do not currently host [cloud provider plugins](https://developer.hashicorp.com/terraform/language/providers) such as  [aws](https://registry.terraform.io/providers/hashicorp/aws/latest), [azurerm](https://registry.terraform.io/providers/hashicorp/azurerm/latest), etc. 

In our research, users reported no interest in creating their own Terraform provider plugins (let alone hosting them). They also saw no value in proxying these plugins from Hashicorp's public registry, as internet access access was inherently required to use Terraform.

However, we're open to implementing support for provider plugins if there's a need; just [let us know](https://forums.inedo.com/).

:::(Internal)
This would  require implementing both the [Provider Registry Protocol](https://developer.hashicorp.com/terraform/internals/provider-registry-protocol) (for first-party plugins) and the [Provider Network Mirror Protocol](https://developer.hashicorp.com/terraform/internals/provider-network-mirror-protocol) (for connectors). Both seem relatively simple, though there appear to be some complexities involving signature files.

In either case, we ought not package these because they are quite large. For example, the `hashcorp\aws` provider for Windows is just a zip file with a single, 628mb .exe. They also have no metadata whatsoever that's returned from the API.

One option is just to store these as manifest-less packages. For example, `hashicorp/aws` packages could be `pkg:tfprovider/hashicorp@5.75.0?os=windows&arch=amd64`. This would be two purls in one feed, which might not work, so it might require a new feed.
:::

## Support for Terraform Backends

A [Terraform Backend](https://developer.hashicorp.com/terraform/language/backend) stores [state files](https://developer.hashicorp.com/terraform/language/state) that Terraform uses to keep track of the resources it manages.  

[ProGet Asset Directories](https://docs.inedo.com/docs/proget/asset-directories-file-storage/what-is-an-asset-directory) can be used as an [HTTP backend](https://developer.hashicorp.com/terraform/language/backend/http) by simply setting `address` as the desired folder in your asset directory.

For example:

```
terraform {
  backend "http" {
    address = "https://proget.corp/endpoints/my-assets/terraform/backends"'
    username = "api"
    password ="abcd12345"
  }
}
```

Note that the username/password is only required if your asset directory is authenticated, and it can alternatively be configured using the `TF_HTTP_USERNAME` and `TF_HTTP_PASSWORD` environment variables.

:::(Internal)
If there was a need/desire for state locking, we would could add `LOCK` and `UNLOCK` verb support to asset directories. These appear to be some kind of WebDAV standard.
:::
