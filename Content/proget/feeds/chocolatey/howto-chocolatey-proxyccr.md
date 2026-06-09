---
title: "HOWTO: Proxy Packages from the Chocolatey Community Repository in ProGet"
order: 4
---

The Chocolatey Community Repository (CCR) is a resource for Chocolatey packages. If organizations want to use the CCR to serve packages to their clients, they set up an internal proxy repository that acts as a go-between between the CCR and the organization's endpoints.

Using an internal proxy repository helps ensure alignment with Chocolatey’s Terms of Use and can reduce the risk of rate limiting, which may temporarily restrict queries and calls to the CCR. If rate limiting occurs, organizations may [need to contact Chocolatey directly](https://chocolatey.org/contact/blocked-ip-address) to have access restored.

With ProGet you can create "Feeds" to proxy packages from the Chocolatey Community Repository (CCR) and install them just as you would when installing them directly from Chocolatey.

## Step 1: Create a New Feed

Start by selecting "Feeds" and "Create New Feed". Next, select "Chocolatey Packages", as we will be creating a feed to proxy Chocolatey packages.

![Create Feed](/resources/docs/proget-chocolatey-newfeed.png){height="" width="50%"}

Now select "Chocolatey Community Packages", which will allow us to proxy packages from the Chocolatey Community Repository.

![Select Feed](/resources/docs/proget-chocolatey-createconnector.png){height="" width="50%"}

From here, name your feed. In this example, we'll call ours `public-chocolatey`. Then select "Create New Feed".

![Name Feed](/resources/docs/proget-chocolatey-nameproxyfeed.png){height="" width="50%"}

ProGet will then redirect you to the newly created `public-chocolatey` feed, which will now be populated with packages proxied from the Chocolatey Community Repository.

![Proxy Feed](/resources/docs/proget-chocolatey-proxyfeed.png){height="" width="50%"}

## Step 2: Creating a Package Approval Flow

In this guide, we covered how to proxy packages from the Chocolatey Community Repository. While proxying simplifies access to open-source packages, organizations may want additional controls over which packages are made available to developers and systems. One way to accomplish this is by implementing a [package approval workflow](/docs/proget/packages/package-promotion).

To learn how to create a package approval flow in ProGet, see [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). Although that guide uses NuGet feeds as an example, the same concepts and workflow can be applied to Chocolatey feeds.

## (Optional) Internalizing Chocolatey Packages

If you want to host Chocolatey packages entirely within your own environment, you can [internalize packages](https://guides.inedo.com/chocolatey-enterprise/internalize/) and store them in a private repository.

Chocolatey's [Package Internalizer](https://docs.chocolatey.org/en-us/features/package-internalizer) can automate the internalization process, though packages can also be internalized manually. For more information, see [HOWTO: Set Up a Private Chocolatey Repository for Internalized Packages](/docs/proget/feeds/chocolatey/howto-chocolatey-internalized).

## Additional Resources

You can visit [Chocolatey's Zendesk Help Center](https://chocolatey.zendesk.com/hc/en-us/articles/43231653511187-Configuring-Chocolatey-Sources-Inedo-ProGet) for more information on configuring Chocolatey sources for your ProGet instance.
