---
title: "HOWTO: Create and Upload Terraform Modules to a Private Registry in ProGet"
order: 2
---

Using ProGet, you can easily set up a private registry to create and upload your internal terraform modules.

This guide will show you how to set up a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet as a private terraform module registry. We will also walk you through packaging your modules and uploading them to this feed.

## Step 1: Create a New Feed

We'll start by creating a Terraform feed that we will use to host our internal terraform packages. 

Navigate to "Feeds" and select "Create New Feed". Then select "Terraform Modules", listed under "System & Software Configuration".

![](/resources/docs/proget-terraform-createfeed.png){height="" width="50%"}

Then select "Private/Internal Terraform Modules" and name the feed, which we will call `internal-terraform`. Then select "Create New Feed"

![](/resources/docs/proget-terraform-createprivate.png){height="" width="50%"}

You will then be redirected to your new `internal-terraform` feed, currently empty.

![](/resources/docs/proget-terraform-emptyprivate.png){height="" width="50%"}

## Step 2: Create a Metadata File

To upload your module to your `internal-terraform` feed, you'll need to package it. You can read more about what goes into a module package on the [Terraform Modules](#terraform-module-packages#terraform-modules) page. To package a module you'll first need to create a Universal Package JSON metadata file. This file, named `upack.json`, provides essential information about your module. The format includes:

```bash
{
  "group": "«company-name»",
  "name": "«module-name»",
  "version": "«version»"
}
```

:::(info) (upack.json Example)
A `upack.json` for the `my-company/my-module/aws` module might look like this:

```bash
{
  "group": "my-company",
  "name": "my-module.aws",
  "version": "4.20.0"
}
```
:::

You can also use additional Universal Package properties like `summary` and `description` to provide additional metadata for users in ProGet.

### Step 3: Package and Upload your Module

Once you have created the metadata file for your module, you'll need to package it. You can do this through ProGet's UI but we recommend using the [`pgutil`](/docs/proget/api/pgutil) command line tool. This will package the module, and also upload it to your `internal-terraform` feed.   


## Step 4: Edit your .tf File

Once you've uploaded a package to your feed, you can add the module to your Terraform configuration by adding this block in your `.tf` file (e.g., `main.tf`):

```
module "«module-name»" {
  source  = "«proget-host-name»/«feed-name»__«namespace»/«module-name»/«provider»"
  version = "«version»"
}
```

:::(info) (Configuration Example)
A `my-company/my-module` module that uses `aws` might look like this:

```
module "example_module" {
  source = "proget.corp/internal-terraform__my-company/my-module/aws"
  version = "4.20.0"
}
```
:::

Once you add this to your `.tf` file, you can add in additional variables and then run `terraform init`.