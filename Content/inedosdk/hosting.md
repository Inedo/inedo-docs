---
title: "Hosting a Private Extensions Feed"
order: 3
---

Typical usage of an Inedo product does not require additional configuration to connect with the Inedo Den that serves extensions for our products. What you may not realize is that extensions are served using a Universal Packages Feed.)

Because of this, Inedo product administrators can host their own "extension gallery" simply by creating a Universal Feed within ProGet, optionally adding a connector to the Extensions Feed https://proget.inedo.com/feeds/Extensions, and updating the Extensions.UpdateFeedUrl setting in the Inedo product as desired.

## Why Host a Private Extensions Feed? 

There are a few benefits to hosting your own extension gallery:

- People in your own organization could [create](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating){target="_blank"} and upload extensions to it
- Extensions in target products could still be updated even without an internet connection
- Inedo extensions could be curated by allowing only "approved" packages
- Multiple extension feeds could be combined in a single one with multiple connectors

## Creating the Private Feed 

To create a private extensions feed in ProGet, select *Feeds* > *Create New Feed*, enter whatever feed name you would like, we used **CustomExtensions**, and click *Create:*

![Name Feed](/resources/docs/inedosdk-feeds-namefeed.png){height="" width="50%"}

## Adding a Connector to the Extensions Feed

Once the feed is created, we will add a connector. From the Manage Feed page, select *Feed Connectors* > *Add Connector* >  *Create Connector* to establish a connection to the Extensions Feed:

![Create Connector](/resources/docs/inedosdk-connectors-configure.png){height="" width="50%"}

Click *Save* on the following screen to associate the newly created connector to inedo.com with the **CustomExtensions** feed.

At this point, we can see the extension packages on the feed page:

![Extension Feed](/resources/docs/inedosdk-feeds-extensionfeed.png){height="" width="50%"}

## Configuring an Inedo Product to use the Private Feed 

To point to the custom extension feed, we must set the **Extensions.UpdateFeedUrl** advanced configuration value to the endpoint URL of the feed:

![Endpoint URL](/resources/docs/inedosdk-feeds-url.png){height="" width="50%"}