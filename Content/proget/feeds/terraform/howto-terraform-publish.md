---
title: "HOWTO: Publish RPM Packages to a Private Repository in ProGet"
order: 2
---

Using ProGet, you can easily set up a private repository to publish and store your internal RPM packages.

This guide will show you how to set up a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet to as a private RPM package repository. We will also walk you through creating your own packages and publishing them to this feed.

## Step 1: Create a New Feed

We'll start by creating a Terraform feed that we will be used to host our internal terraform packages. 

Navigate to "Feeds" and select "Create New Feed". Then select "Terraform Modules", listed under "System & Software Configuration".

![](/resources/docs/proget-terraform-createfeed.png){height="" width="50%"}

Then select "Private/Internal Terraform Modules" and name the feed, which we will call `internal-terraform`. Then select "Create New Feed"

![](/resources/docs/proget-terraform-createprivate.png){height="" width="50%"}

You will then be redirected to your new `internal-terraform` feed, currently empty.

![](/resources/docs/proget-terraform-emptyprivate.png.png){height="" width="50%"}


## Step 2: Package Your Modules

Before using a Terraform Feed as a Private Module Registry, you'll need to package your modules. This is as simple as zipping the content and uploading it to the ProGet UI or using the [`pgutil`](/docs/proget/api/pgutil) CLI; see [Terraform Module Packages](#terraform-module-packages) to learn more.

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

















## Step 5: Add the Feed to Your Local RPM Environment

To install packages from the `internal-rpm` feed, you'll need to add it as a source in the local RPM environment. For this, you will need feed's URL. This can be found at the top right of the feed's page.

![](/resources/docs/proget-rpm-internal-url.png){height="" width="50%"}

To add the feed, you'll need to create a `.repo` file locally. Create the file by entering:

```bash
$ sudo vi /etc/yum.repos.d/internal-rpm.repo
```

In this case we used the `vi` text editor, but you can use any other such as `nano`. With the `.repo ` file open, enter the following:

```bash
[internal-rpm]
name=InternalRpm
baseurl=http://proget.corp.local/rpm/internal-rpm/ # your RPM feed URL
enabled=1
gpgcheck=0
```

Then save and exit (`:wq` in the case of `vi`). You can confirm the feed has been set by listing the configured repositories:

```bash
$ yum repolist all
```

Or listing packages by entering:

```bash
$ yum list available --disablerepo="*" --enablerepo=internal-rpm
```

By default, repositories will already be configured, depending on the distribution of your local environment. We recommend removing these to install packages exclusively from your `internal-rpm` feed. You can remove a repository by entering:

```bash
$ sudo rm /etc/yum.repos.d/«repo-name».repo
```











## Uploading Packages to ProGet

Before using a Terraform Feed as a Private Module Registry, you'll need to package your modules. This is as simple as zipping the content and uploading it to the ProGet UI or using the [`pgutil`](/docs/proget/api/pgutil) CLI; see [Terraform Module Packages](#terraform-module-packages) to learn more.

Once you've uploaded a package to your feed, you can add the module to your Terraform configuration using the following format:

```
module "«module-name»" {
  source  = "«proget-host-name»/«feed-name»__«namespace»/«module-name»/«provider»"
  version = "«version»"
}
```

Once you add the module to your configuration, you can add in additional variables and then run `terraform init`.









## Step 2: Create an API Key

Now create an [API Key](/docs/proget/api/apikeys) which will allow your local RPM environment to authenticate to the `internal-rpm` feed to publish packages to it, as well as install them once published.

You can read more about creating API keys in ProGet on our [API Key](/docs/proget/api/apikeys) page.

When creating an API Key, fill in the fields by selecting "Feeds (Use Certain Feeds)" as the "Feed Type" and selecting the `internal-rpm` feed. Then set the API key. You can use any alphanumeric sequence, or just leave it blank to autogenerate one.

![Create RPM API Key](/resources/docs/proget-rpm-apikey.png){height="" width="50%"}

Make sure the "View/Download" and "Add/Repackage" boxes are checked, and then select "Save".


