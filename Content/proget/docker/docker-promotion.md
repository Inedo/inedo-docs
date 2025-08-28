---
title: "Docker Image Promotion"
order: 5
---


Docker Image Promotion is the process of promoting Docker Images between registries to ensure that only approved and verified images are used in the right environments, such as production. 

In ProGet, images can be promoted from one registry to another and tracked throughout the process. This allows you to build a promotion pipeline that allows you to maintain separate levels of quality in different registries (such as Development, Production, etc).


::: (INFO)
This feature is limited in ProGet Free. Compare [features by edition](/docs/proget/administration/license) or [request a quote](https://inedo.com/proget/pricing/request-quote). 
:::

## How to Promote an Image
There are two ways to promote an image, using the _Promote_ button on the repository's tag page in ProGet or an API call. Any images that are promoted to a target registry are __not__ deleted from the source registry.  

Currently, images that use ["fat" manifests](https://docs.docker.com/registry/spec/manifest-v2-2/) cannot be promoted. You will need to recreate and push those images through the docker CLI to your target registry.

### Creating a Validated/Promoted Docker Registry 

Any registry can be used as a validated/promoted Docker registry, but best practice is to create a separate registry that contains only promoted internal images and verified external images. When creating your new Docker registry, select the _validated/promoted_ feed usage type.

For additional guidance—especially if you’re using Artifactory—see [How to Manage Docker Repositories in ProGet for Artifactory Users](https://blog.inedo.com/proget-migration/how-to-manage-docker-repositories-in-proget-for-artifactory-users/) on the Inedo Blog.

#### Promoting Images Using The Repository's Tag Page 

To promote an image using the repository's tag page, first navigate to the registry, repository, and tag you would like to promote. Hover over the _▼-button_ in the upper right corner, and click the _Promote_ button.

::: (INFO)
Image Promotion via the repository's tag page is limited in ProGet Free. When using ProGet Free, you can promote images, but only through this page, and you won't be able to see who did it, when, or why.
:::

#### Promoting Images Using API 

To promote an image via the API you will first need to generate an API key that grants access to Package Promotion. Then visit our [Package Promotion Endpoint](/docs/proget/packages/package-promotion) guide in our [ProGet API Reference](/docs/proget/api) section to familiarize yourself on how to work with ProGet APIs and the Promote Packages endpoint.

::: (INFO)
Image promotion via the API is not availabile in ProGet Free.
:::

### Creating an Image Promotion Pipeline 

An image promotion pipeline helps you to enforce the process of elevating images between your different registries/environments by selecting a specific target registry. Once a target registry is selected to promote to, both the repository's tag page and the [Package Promotion Endpoint](/docs/proget/packages/package-promotion) will only allow you to promote images to the selected target registry.

To configure the pipeline, you will need to:
1. Navigate to the source registry containing the images to be promoted  
2. Click on the _Manage Feed_ button in the upper right corner of the page
3. Click the _configure_ link to the right of the _Promote To Feed_ property
4. Select the target promotion registry

**Note:** A validated/promoted Docker registry can be a source registry for a different validated/promoted Docker registry. This allows you to keep environments separated into different registries and enforce an elevation process across registries.

## Package Promotion Security

Promote Packages is a [built-in task](/docs/proget/administration-security) in ProGet. This task allows access to promote images to a specified registry. Users granted this task should also be granted at least the View & Download Packages task for the source registry. 

You can also create a [Custom Task](/docs/proget/administration-security/creating-tasks) by going to `Administration > Users & Tasks > Customize Tasks`. To give or deny users/groups permissions to this task go to the Users & Tasks page.

## Track Your Image Promotions 

Image promotion tracking is viewable in the _History_ tab on the repository's tag page.  The history will show a _Promoted_ action that includes:
- When the promotion occurred
- Who promoted the image
- The source registry
- The target registry
- Any comments left by the promoter

|Action|On|Detail
|-----|-----|------
Promoted|5/14/2020 1:30:15 PM by Admin|from [devdocker](/) to [rcdocker](/)<br/>Ready for testing
Promoted|5/15/2020 1:30:15 PM by API|from [rcdocker](/) to [proddocker](/)<br/>Release 12.0.5

