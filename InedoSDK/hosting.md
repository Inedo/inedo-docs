---
title: Hosting a Private Extensions Feed
sequence: 30
keywords: inedo, inedo sdk
show-headings-in-nav: true
---

Typical usage of an Inedo product does not require additional configuration to connect with the Inedo Den that serves extensions for our products. What you may not realize is that extensions are served using a [Universal Feed](/docs/upack/feed-api/endpoints).

Because of this, Inedo product administrators can host their own "extension gallery" simply by creating a Universal Feed within ProGet, optionally adding a connector to the Inedo Den https://inedo.com/den/feed, and updating the Extensions.UpdateFeedUrl setting in the Inedo product as desired.

## Why Host a Private Extensions Feed? {#why-host data-title="Why Host a Private Extensions Feed?"}

There are a few benefits to hosting your own extension gallery:

{.docs}
- People in your own organization could [create](/docs/inedosdk/extending/creating) and upload extensions to it
- Extensions in target products could still be updated even without an internet connection
- Inedo extensions could be curated by allowing only "approved" packages
- Multiple extension feeds could be combined in a single one with multiple connectors

## Creating the Private Feed {#creating-feed data-title="Creating the Private Feed"}

To create a private extensions feed in ProGet, select *Feeds* > *Create New Feed*, enter whatever feed name you would like, we used **CustomExtensions**, and click *Create:*

![Creating Private Feed](/resources/documentation/various/creating-custom-feed.png){.screenshot}

## Adding a Connector to the Inedo Den {#adding-connector data-title="Adding a Connector to the Inedo Den"}

Once the feed is created, we will add a connector. From the Manage Feed page, select *Feed Connectors* > *Add Connector* >  *Create Connector* to establish a connection to the Inedo Den:

![Add Connector to Den](/resources/documentation/various/den-connector.png){.screenshot}

Click *Save* on the following screen to associate the newly created connector to inedo.com with the **CustomExtensions** feed.

At this point, we can see the extension packages on the feed page:

![CustomExtensions Feed](/resources/documentation/various/extension-feed.png){.screenshot}

## Configuring an Inedo Product to use the Private Feed {#configuring data-title="Configuring a Product to use the Private Feed"}

To point to the custom extension feed, we must set the **Extensions.UpdateFeedUrl** advanced configuration value to the endpoint URL of the feed:

![Endpoint URL](/resources/documentation/various/endpoint-url.png){.screenshot}
