---
title: "HOWTO: Content Distribution for Edge Computing"
order: 3
---


Organizations with edge computing architectures need to constantly transfer content such as system packages (Debian, RPM, Chocolatey), Docker images, and other files to their edge sites.

[ProGet Edge Computing Edition](https://inedo.com/proget/edge) can help with edge computing distribution thanks to its replication feature. This allows different sites to share content without having to set up a complicated global infrastructure.

This guide explains how to use the replication feature to create a default "hub and spoke" configuration in ProGet to easily distribute content to other servers.

![progetenterprise-hubspoke-diagram.png](/resources/docs/progetenterprise-hubspoke-diagram%283%29.png)

## Feed Replication

Feed replication can be very granular in ProGet. This guide covers a specific configuration that is best suited for Edge Computing (e.g., distributing from one main server to many local servers). Our [Feed Replication documentation](/docs/proget/replication-feed-mirroring/proget-advanced-feed-replication) covers other possible configurations in ProGet.

In this guide, we focus on a "Hub and Spoke" model, where the Source server is the "Hub" and the destination/edge servers are the "Spokes." In this model, content is transferred in one direction from the hub to the spoke.

Each server in a group can change the direction of communication, depending on its configuration. However, this guide is specifically about configuring a source server to only push packages out.

:::(Warning)
Please note that a ProGet Enterprise license is required for the replication function.
:::

:::(Info)
These steps/screenshots in this guide are based on the replication of ProGet v2022 UI, which is available as a preview feature in ProGet v6.0.11+. However, the concepts are the same in earlier versions.
:::

## Step 1: Designate your Hub server and Spoke servers

This will help avoid confusion throughout the guide.

Your Hub is your Source server; the main server that teams or developers push to first. Your Spokes are your Target Edge locations that you want to deploy to.

## Step 2: In the Hub server, in ProGet, navigate to Replication

In ProGet of your Hub server, navigate to the Replication page via the top ribbon.

Once there, click [Configure New Replication].

![proget_replicationoverview.png](/resources/docs/proget_replicationoverview%281%29.png)

A pop-up will appear with “Configuration Replication" options. 

## Step 3: Configure Hub server for Incoming replication

In the pop-up, select the Feed you want to replicate from the drop-down selection, then click [Communication Mode].

![proget_replicationconfig_feeds.png](/resources/docs/proget_replicationconfig_feeds%281%29.png)

On the Communication Mode tab, select the communication type "Incoming (other ProGet instance may connect to this instance)"

* Incoming is a "read-only" state; Spokes can connect to the Hub and pull changes, but cannot push back changes.

Set the "Authentication Mode" to "Requires a specific sync token".

![proget_replicationconfig_communication.png](/resources/docs/proget_replicationconfig_communication%281%29.png)

:::(Info)
The Hub server should be set to Incoming Replication, as it's easier to configure our Spoke servers.

Otherwise, you would need to add each Spoke to the Hub server as Outgoing Replication.
:::

## Step 4: Generate a Sync Token and Save the Source URL

Once the Communication and Authentication options are selected, the “Sync token” line item will appear.

Click [generate] and copy this token for Step 8. 

![proget_replicationconfig_communication_synctoken.png](/resources/docs/proget_replicationconfig_communication_synctoken%281%29.png)


Click [Replication Mode] to continue. 

## Step 5: Set the Replication Mode of the Hub Server

In the same popup, on the Replication Mode tab, select the type of replication for your server.

In this tutorial, the hub server should be read-only so that it does not poll the Spokes for changes.

Set the mode to "Push Content to Other Instance".

![proget_replicationconfig_replication.png](/resources/docs/proget_replicationconfig_replication%281%29.png)

Click [Summary].

Confirm your previous choices on the summary page. If satisfied, click [Add New Replication].

## Step 6: In the Spoke server, create a new feed

Create a new feed on the Spoke server that has the same type as the Hub server feed from steps 2 - 5.

The names do not have to be identical.

## Step 7: In the Spoke server, navigate to Replication.

As in step 2, navigate to the Replication page in ProGet of your Spoke server using the top ribbon.

Once there, click [Configure New Replication].


## Step 8: Configure the Spoke server for Outgoing Replication

In the pop-up window, select the feed you want to replicate from the Hub server.
![proget_replicationconfig_spokefeeds.png](/resources/docs/proget_replicationconfig_spokefeeds%281%29.png)

Continue by clicking [Communication Mode].

On the next page, select "Outgoing (routine connection to another ProGet instance)".

The page will populate with more options. 

![proget_replicationconfig_spokecommunication.png](/resources/docs/proget_replicationconfig_spokecommunication%281%29.png)

Once the Outgoing optionis selected, paste the base URL of the hub server and the sync token generated in step 4.

:::(Warning) (Other feed names)
If the name of your Hub feed and the name and Spoke feed are the same, make sure this option is enabled.

If your Hub feed name and Spoke feed name are *not* the same, as in this tutorial, uncheck this option and enter the feed name for "Name for nuget-replicate" (e.g. the Hub feed name).
:::

Click [Replication Mode]

## Step 9: Set the Replication Mode in the Spoke Server

On the Replication Mode page of the pop-up, select the replication mode for this server.

In this tutorial, we want the Spoke server to query the Hub feed for changes and pull only changes without affecting the Hub.

Set the mode to "Pull Content from Other Instance"

![proget_replicationconfig_spokereplication.png](/resources/docs/proget_replicationconfig_spokereplication%281%29.png)

Click [Summary].

Confirm your previous choices on the summary page. If satisfied, click [Add New Replication].

The two servers are now configured. The Spoke server will now periodically check the Hub for packages and replicate.

## Step 10: (Optional) Check the Connection

You can confirm that the two servers were successfully connected by manually performing a replication.

On the Spoke server, switch to the Replication tab.

Click [run] at the top right of the Replication Summary page.

![proget_replicationconfig_spokeconfirm.png](/resources/docs/proget_replicationconfig_spokeconfirm%281%29.png)

This manually runs a replication. 

Browse your feed and you will see packages from the Hub server have been replicated into the Spoke server.

You can also view the results of a run via the log history in the Spoke server. 

## Step 11: (Optional) Add more Spoke servers

You can now add as many spoke servers as you like to replicate the Hub Server.

Repeat steps 6 through 9 for the Spoke servers you want to add.

You don't need to change anything on the Hub server.

## Multi-Hub Configurations

You may work in an organization that relies on multiple Source servers and/or feeds (e.g., a "Secondary Hub") around the world to distribute content to multiple Target servers and/or feeds.

A multi-hub configuration has two Source feeds (e.g., a USA and a ASIA Source feed) and can be configured using the Replication feature in ProGet Enterprise with the same steps as above.

A Secondary Source feed would need to be configured for both Incoming ** and** Outgoing Replication, such that it can, for example, both replicate the Main Source Feed (Outgoing) and allow Target Feeds to replicate it (Incoming).

![progetenterprise-multihub-diagram.png](/resources/docs/progetenterprise-multihub-diagram%283%29.png)

There are many possible configurations using the Feed Replciation feature in ProGet, like complete mirroring of a single feed across multiple ProGet instances. Check out our [Feed Replication documentation](/docs/proget/replication-feed-mirroring/proget-advanced-feed-replication#architecture-examples) to learn about other possible configuration set-ups. It's also possible to [set up a feed exclusively for disaster recovery](/docs/proget/replication-feed-mirroring/proget-howto-utilize-feed-replication-for-disaster-recovery) purposes. 

## Troubleshooting
### Issue: Outgoing is not replicating

Despite following the steps above, it is possible that the Spoke server does not replicate the content from the Hub server.

You will need to navigate to the settings to confirm this: Feed Replication Failed error is displayed in the diagnostic center.

![proget-feedreplicationerror-feedreplicationfailed.png](/resources/docs/proget-feedreplicationerror-feedreplicationfailed.png)

It's possible that there was an error entering the Sync token or the source URL. Make sure that both were copied correctly from the Hub server to the Spoke by repeating steps 4 - 8.