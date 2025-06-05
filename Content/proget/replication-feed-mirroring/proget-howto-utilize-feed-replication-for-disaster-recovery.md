---
title: "HOWTO: Disaster Recovery with Replication"
order: 2
---


Teams looking to fortify their disaster recovery plans can leverage ProGet’s [replication feature](/docs/proget/replication-feed-mirroring/proget-advanced-feed-replication) to create feeds to use in an emergency situation.

ProGet can replicate a feed that can be used in disaster situations such as a fire, tornado, long-term power outage, etc. that would cause your production server to become unusable.

Replicated feeds can be filled with business-critical packages that your company cannot do without in the event of a disaster. After setting up a disaster recovery feed with all business-critical packages, teams can continue to operate at minimal capacity until the production server is up and running again.

This document describes an example of a company, Kramerica, using ProGet's replication feature to replicate a feed to a disaster recovery server. Kramerica has two servers running two instances of ProGet Enterprise:
-	Production server in Ohio.
-	Disaster recovery server in Florida.

:::(Info) (Dark Mode)
To illustrate the difference between the two instances, the screenshots of Kramerica's Disaster Recovery instance are shown in dark mode.
:::

## Step 1: Plan What Feeds You Need
A disaster recovery server can be thought of as a bunker. It should be nice, but it doesn't have to have all the comforts of home. Creating an exact duplicate of a production server would be expensive, difficult to maintain, and a time suck.

In general, teams should only replicate feeds to a disaster recovery server that:
-	Are critical to business operations
-	Would be required for recovery in the event of a production server failure

For example, Kramerica will **not** replicate feeds such as: ci-apps, testing-apps, unapproved-nuget, approved-nuget, pre-release nuget, etc. The reason is that these feeds aren't considered business-critical and are mostly filled with packages that are still under development.

Of course, these feeds could be replicated—but that would mean that every time a new version of a package is created or a package is changed, everything would have to be recreated. This is simply not necessary, especially for Kramerica's "bunker" scenario.

Duplicating the environment would mean Kramerica would have to pay for a bigger, faster,  [load-balanced](/docs/installation/high-availability-load-balancing/high-availability-load-balancing) server. Something that isn't in their budget. Instead, Kramerica only plans to replicate a feed to their ProGet disaster recovery instance: kramerica-internal-nuget.

![Internal Feed](/resources/docs/proget-replication-internalfeed.png){height="" width="50%"}

## Step 2: Create Disaster Recover Feed
First, connect to your ProGet instance on a disaster recovery server, ideally located elsewhere.

In our example, we create a NuGet feed and name it `kramerica-internal-nuget` just like the production server. That way, when it comes time to use the disaster recovery feed, we can simply change the DNS to point to the disaster recovery server.

![Create Disaster Recovery Feed](/resources/docs/proget-replication-namerecoveryfeed.png){height="" width="50%"}

Note that we created a "private/internal" feed because we don't want this feed to connect to a public repository like [NuGet.org](https://www.nuget.org/){target="_blank"}. 

By default, **all** packages are replicated from one feed to another. To ensure that only necessary packages are stored in the disaster recovery feed, teams can set up more aggressive retention rules. As explained in Step 1, our disaster recovery server shouldn't and doesn't need to be large or powerful. The less storage it requires, the better.

To set up retention rules, navigate to your Disaster Recovery Feed > "Manage Feed" > "Storage & Retention" > add

Here teams can set up retention rules that fit their current disaster recovery plans and server capabilities.

In this example, we'll create rules that instruct ProGet to delete pre-release versions, any versions older than the last 10, and any unused versions that haven't been requested in the last 30 days.

![Configure Retention Rules](/resources/docs/proget-replication-retentionrules.png){height="" width="50%"}

## Step 3: Configure Production Feed for Replication
Now that our disaster recovery feed is set up, we can configure our production feed for replication.

To do this, access your production server, click on "Replication" > "Configure New Replication" and select the feed you want to replicate.

![Add Production Feed](/resources/docs/proget-replication-addproductionfeed.png){height="" width="50%"}

We want our production feed to be replicated and want to make sure it's not inadvertently altered by other feeds. To do this, we select "Incoming" and create a special sync token that is shared only with the disaster recovery feed.

![Configure Replication](/resources/docs/proget-replication-configureproductionfeed.png){height="" width="50%"}

Feed replication can be used for many use cases like [Edge Computing](/docs/proget/replication-feed-mirroring/proget-howto-replicate-edge-locations) or [Federated Architecture](/docs/proget/replication-feed-mirroring/proget-howto-federated-development). However, to properly configure a feed for Disaster Recovery, we will set the Replication mode to "Push Content to Other Instances." 

![Replication Mode](/resources/docs/proget-replication-productionfeedmode.png){height="" width="50%"}

To complete the configuration, confirm your configuration settings and click on "Add New Replication" Finally, click on the ProGet logo to navigate to your home page and copy your URL. We will use this URL in the next step to establish communication with our disaster recovery feed.

Now that our production feed is configured, any number of feeds can connect and replicate it.

## Step 4: Configure Disaster Recovery Feed for Replication
The disaster recovery feed must now be configured to replicate the production server.

To do this, access your disaster recovery server, click Replication > Configure New Replication, and select the feed that will replicate the production feed. 

![Add Recovery Feed](/resources/docs/proget-replication-addproductionfeed.png){height="" width="50%"}

Next, we configure this feed for outbound communication and enter the URL from our production server and the sync token generated in the previous step. Since our two feeds are named `kramerica-internal-nuget`, we check the Other feed names checkbox.

![Configure Replication](/resources/docs/proget-replication-configure-recoveryfeed.png){height="" width="50%"}

To ensure that packages are properly replicated from our production feed to the disaster recovery feed, we select "Pull content from other instances"

![Replication Mode](/resources/docs/proget-replication-recoveryfeedmode.png){height="" width="50%"}

After reviewing the configurations, click "Add New Replication" and your disaster recovery feed is fully configured.

 
## Step 5: Test Run Replication
After configuring your disaster recovery feed, you'll be redirected to the Replication Overview.

By default, replication runs every 60 seconds. We could simply wait for replication to run automatically. However, in this example, Kramerica wants replication to run immediately to verify success. For this purpose we click on "Run" button

![Run Replication](/resources/docs/proget-replication-run.png){height="" width="50%"}

Click "Run all replications now" to start replication immediately.

Now all packages from the production server feed are in our disaster recovery feed.

## Step 6: Test Run Retention
After you perform replication, you should also test whether the retention rules you set for the disaster recovery feed work.

To do this, go to "Admin" > "Additional Logs & Events"
 "Scheduled Jobs" and press the green "Play" button next to the feed you want to play.

![Run Feed Cleanup](/resources/docs/proget-replication-feedcleanup.png){height="" width="50%"}

## Verify Successful Replication

For detailed history, including the names of replicated packages, click on "View last run". Here you can view the full run details, including the names of the packages.

![Execution Details](/resources/docs/proget-replication-executiondetails.png){height="" width="50%"}

## Step 7: Test Disaster Recovery Plan
Of course, this varies from team to team. But in general, you should test your disaster recovery plan by migrating an existing ProGet installation to a new server and deploying from there.

Kramerica will test its disaster recovery plan by setting up a usable ProGet instance with the packages available in its disaster recovery feed. They will then attempt to deploy from the newly established instance, which would serve as the new production server in a real crisis situation.

After successful deployment to the new production server, Kramerica performs a one-time replication from the disaster recovery feed to the new production feeds.

-	Production Server Feed One-Time Replication Configuration
    -	Outgoing options: Only apply external changes to local feed
    -	Incoming options: Disabled

-	Disaster Recovery Feed One-Time Replication Configuration
    -	Outgoing Replication: Disabled
    -	Incoming Replication: Allow external feeds to replicate from this feed

Once it's confirmed that all Kramerica mission-critical packages are present on both the disaster recovery and production servers, we switch the inbound and outbound connection settings to the values configured in steps 3 and 4.
