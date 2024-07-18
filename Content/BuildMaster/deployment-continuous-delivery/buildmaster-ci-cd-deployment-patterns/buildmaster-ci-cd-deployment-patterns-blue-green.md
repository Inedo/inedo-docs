---
title: "Blue-Green Deployments"
order: 1
---

Blue-green deployments are a Continuous Delivery pattern designed to effectively eliminate deployment downtime and make rollbacks nearly instantaneous. This is done by having two nearly identical production environments: *Blue* and *Green*.

::: (info) (See it live!)
You can create a *Blue-Green Deployment Sample* application on your own instance of BuildMaster by just going to the Create New Application page. This sample will show you how you can implement Blue-Green deployments in your own applications.
:::

[Martin Fowler](http://martinfowler.com/bliki/BlueGreenDeployment.html) explains this in more detail:

> One of the challenges with automating deployment is the cut-over itself, taking software from the final stage of testing to live production. You usually need to do this quickly in order to minimize downtime. The blue-green deployment approach does this by ensuring you have two production environments, as identical as possible. At any time, one of them, let's say blue for the example, is live. As you prepare a new release of your software you do your final stage of testing in the green environment. Once the software is working in the green environment, you switch the router so that all incoming requests go to the green environment—the blue one is now idle.

From an end-user's perspective, there really is no Blue or Green environment: just a single Production environment. The blue-green deployment model looks something like this:

![deployments.png](/resources/docs/deployments.png){height="" width=""}

From IT's perspective, there are indeed two environments (Production-Blue and Production-Green), and different releases will be deployed to these environments:

![deployments-by-release.png](/resources/docs/deployments-by-release.png){height="" width=""}

## Benefits of Blue-green Deployments

The primary benefit to using blue-green deployments is zero downtime, but there are three additional benefits:

1. **No need for a dedicated staging environment.** Your Blue and Green environments will serve as a rotating staging-and-production environment, so you won't need to worry about errors arising from differences between production and staging environments; they will both be considered production.

2. **Instantaneous rollback after problems.** If you discover a problem after going live (i.e. swapping your Blue and Green environments), you can rollback by simply performing another environment swap. The old code will already be up and running on the opposite environment.

3. **Simple disaster recovery**. You will already have two nearly identical environments. You can simply use one of those as a disaster recovery. After you've decided that you don't need to rollback, you can simply deploy the new release to the other environment. This will give you a standby environment in case of disaster.

## How to Perform Blue-green Deployments in BuildMaster

We created a blue-green sample application that you can create in your own instance of BuildMaster. This application lets you visualize which release are Blue and Green (or undecided) as well as shows you what's in Blue and Green:

![application-overview.png](/resources/docs/application-overview.png){height="" width=""}

These steps will be based off of this application, but BuildMaster is very flexible, so you can customize the approach however your environments require.

### Initial BuildMaster Configuration

 - Create a standard application with a normal `Build` and `Deploy` script
 - Create `Production-Green` and `Production-Blue` as child environments of Production
 - Create a `SwapBlueGreen` script that uses a variable called `$SwapTo` to determine whether to swap Blue to Green, or Green to Blue
     * This script will issue instructions to your router or load-balancer using PowerShell or another mechanism
 - Create two [pipelines](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines):
     * Both will start with your standard pre-production stages (Build, Integration, Test, etc.)
     * Blue will deploy to a Blue stage, then a Swap stage
     * Green will deploy to a Green stage, then a Swap stage
     * Use a `$SwapTo` pipeline stage variable to indicate whether to swap to Green or Blue

### Option 1: Create Blue- and Green-targeted Releases

Once you've prepared your application as described above, you can create a new release and select the `Blue` or `Green` pipeline as appropriate.

### Option 2: Decide Blue or Green Later

You may not know whether a release will target Blue or a Green until fairly close to release date. In this case, using a Blue or Green pipeline will be misleading because it will imply that the target has already been decided.

While you can always change the release's pipeline at anytime, you should consider creating a third pipeline (BlueGreen) that implies that descision hasn't been made yet. This pipeline should be configured as follows:

 - use the exact same pre-production (Build, Integration, Test, etc.) stages
 - create a final stage called Staging 
 - disable the pipeline option to deploy releases once it reaches the final stage

Once a build reaches Staging, you can simply swap the releases pipeline to Blue or Green and continue deploying.

You can even simplify this process and give the user a choice when they deploy to Staging. To do this:

 - creating a pipeline called Choose Blue or Green
 - configure a Deployment Variable Template for the Staging stage (List: Blue, Green)
 - create a script that changes the release's pipline to the selected variable

The sample application has this pipeline and script already configured for you to see.

## How to Roll Back Blue-green Deployments

If anything goes wrong after deploying, blue-green deployments make it very easy to quickly roll back. Instead of having to redeploy the entire build and all of the artifacts, you can just perform another environment swap.

Because the last stage of both the Blue and the Green pipelines simply swaps environments, you can just navigate to the build that was deployed in the opposite environment and redeploy it.

![application-overview \(1\).png](/resources/docs/application-overview%20%281%29.png){height="" width=""}

For example, in the above image, a Green-targeted build was just deployed. To roll back, simply navigate to the build in Production-Blue (1.0.1 - Build 1) and redeploy. This will execute the environment swap and immediately restore the previous release.


## Using Blue-green in Disaster Recovery Scenarios

Because your Blue and Green environments will have nearly identical hardware, they can also serve as a production-and-disaster recovery pair. After deploying to one environment (say Blue) and verifying that it's stable and won't be rolled back, you can simply deploy the same release to the other environment (Green).

This involves adding another stage to each of the pipelines after the Swap stage.