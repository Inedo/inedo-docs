



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


## Terraform Module Packages

Terraform Modules are not distributed using [self-describing package files](/docs/proget/packages/what-is-a-package); instead, a Terraform module registry (e.g. `registry.terraform.io`) is simply a list of versions and a "pointer" to a GitHub repository where the files can be downloaded. 

While this makes things simpler for publishers to the free and open-source Hashicorp registry, it's not a good fit for organizations that rely on reliable and repeatable processes. For example, the GitHub repository could be deleted or changed, meaning the module will suddenly no longer be available.

Terraform Feeds in ProGet store modules in self-describing [Universal Packages](/docs/proget/feeds/universal/universal-packages). This not only makes the modules portable and independent from external resources, but cryptographically seals them.

### Creating Terraform Module Packages

A Terraform Module Package is a specially-formatted ZIP file:

1. the file has a .upack file extension (not .zip)
2. there is a manifest file in the root directory named `upack.json` with the following properties
   a. `group` is used for the module's namespace
   b. `name` is formatted with 2-parts (`«module-name».«provider»`)
   c. `version` is a valid, [3-part semantic version number](https://semver.org/)
3. The `/package` directory in the zip file contains the module's content


You can create a Terraform Module Packages from the ProGet UI or using the [`pgutil`](/docs/proget/api/pgutil) commandline tool to [create and upload](/docs/proget/api/universal-feed/upload) them to your ProGet instance.

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

