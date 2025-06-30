---
title: "HOWTO: Proxy Terraform Modules from the Hashicorp Registry in ProGet"
order: 1
---

With ProGet, teams can set up ["Feeds"](/docs/proget/feeds/feed-overview) to use as private repositories for [Terraform](https://developer.hashicorp.com/terraform) modules, proxied from external registries like the [Hashicorp registry](https://registry.terraform.io/) using ["Connectors"](/docs/proget/feeds/connector-overview).

These feeds also cache modules locally, so if an external registry goes offline, your team can still access the versions they've used before. ProGet also provides visibility into which modules are being consumed, helping teams identify popular or potentially risky dependencies.

On this page, we’ll cover how to create a Terraform feed in ProGet, set up connectors to pull in modules from one or more upstream sources, and configure your local Terraform setup to use the feed as a module registry. 

## Step 1: Create a New Feed

We'll start by creating a Terraform feed that will proxy packages from the [Hashicorp registry](https://registry.terraform.io/).

Navigate to "Feeds" and select "Create New Feed". Then select "Terraform Modules", listed under "System & Software Configuration".

![](/resources/docs/proget-terraform-createfeed.png){height="" width="50%"}

Then select "Free/Open Source Terraform Modules".

![](/resources/docs/proget-terraform-connect.png){height="" width="50%"}

## Step 2: Connect to the Hashicorp Registry

Next, name your feed. For the example, in this guide we will call our feed `public-terraform`. Make sure that the "Create a connector to registry.terraform.io" checkbox is selected, and select "Create New Feed".

![](/resources/docs/proget-terraform-namefeed.png){height="" width="50%"}

Your `public-terraform` feed will then be created, populated with proxied Terraform Modules from the Hashicorp Registry.

![](/resources/docs/proget-terraform-publicfeed.png){height="" width="50%"}

## Step 3: Update Terraform Configurations { #update-configuration }

Now, you'll need to update your Terraform `.tf` files to specify module sources and versions, directing Terraform to fetch the module from your `public-terraform` feed instead of the public Hashicorp Registry.

```bash
module "«module-name»" {
  source  = "«proget-host-name»/«feed-name»__«namespace»/«module-name»/«provider»"
  version = "«version»"
}
```

:::(info) (Example:)
To fetch the the `vpc` module for the `aws` provider you would enter:

```bash
module "my_vpc" {
  source  = "proget.corp.local/public-terraform/vpc/aws"
  version = "1.0.0"
}
```
:::

You'll then need to run `terraform init` in your project directory to authenticate your ProGet feed and download the specified module. You can also run `terraform plan` to make sure the module is correctly referenced.

## (Optional) Authenticating to Your Terraform Feed

By default your `public-terraform` feed does not require authentication and can be viewed anonymously by anyone. However, you may want to make your feed private and [configure it to require authentication to access](/docs/proget/feeds/terraform#authentication). For example, when also hosting your own internal packages.

## (Optional) Creating a Package Approval Flow

On this page, we discussed how to proxy packages from the Hashicorp Registry. However, this approach allows developers to fetch any OSS module without oversight for quality or compliance. It's advisable to integrate an approval process in both development and production environments. You can establish this by implementing a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

For instructions on setting up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). While this guide focuses on NuGet feeds, the steps are identical for creating Terraform package feeds.

Once you've created the "approved" feed for promoting your Terraform modules, proceed to integrate it into your local Terraform environments by following the steps in ["Update Terraform Configurations"](#update-configuration).