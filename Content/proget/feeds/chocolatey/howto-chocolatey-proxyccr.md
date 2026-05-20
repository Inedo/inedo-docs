---
title: "HOWTO: Set Up a Proxy Feed of the Chocolatey Community Repository"
order: 4
---

The Chocolatey Community Repository (CCR) is a resource for Chocolatey packages. If organizations want to use the CCR to serve packages to their clients, they must create an internal proxy repository that acts as a go-between between the CCR and the organization's endpoints.

Using an internal proxy repository prevents the organization from breaking Chocolatey's Terms of Use, possibly leading to the revocation of their Chocolatey for Business (C4B) license and legal action. It also prevents the organization from becoming rate limited, which places a block on queries and calls to the CCR. If this occurs, an organization [must reach out to Chocolatey](https://chocolatey.org/contact/blocked-ip-address) in order to have the block lifted.

## Create an Internal Proxy Repository in ProGet

In your ProGet instance, select "Feeds" and "Create New Feed", and select "Chocolatey Packages".

![Create Feed](/resources/docs/proget-chocolatey-newfeed.png){height="" width="50%"}

Now select "Free/Open Source Chocolatey Packages" to create the internal proxy repository.

![Select Feed](/resources/docs/proget-chocolatey-createconnector.png){height="" width="50%"}

From here, we name our feed. For this example, we will call it `public-chocolatey`, and then click "Create New Feed".

![Name Feed](/resources/docs/proget-chocolatey-nameproxyfeed.png){height="" width="50%"}

## Additional Resources

You can visit Chocolatey's Zendesk Help Center for more information on how to configure Chocolatey sources for your ProGet instance. [Click here to get started.](https://chocolatey.zendesk.com/hc/en-us/articles/43231653511187-Configuring-Chocolatey-Sources-Inedo-ProGet)
