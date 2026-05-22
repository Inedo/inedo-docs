---
title: "HOWTO: Proxy Packages from the Chocolatey Community Repository in ProGet"
order: 4
---

With ProGet you can create "Feeds" to proxy packages from the Chocolatey Community Repository (CCR) and install them just as you would when installing them directly from Chocolatey.

Using ProGet as a proxy will cache packages locally, allowing teams to continue accessing packages even if the Chocolatey Community Repository is unavailable. Organizations using the CCR are also encouraged to use an internal proxy repository to help comply with Chocolatey's Terms of Use and avoid potential rate limiting issues that can restrict access the the repository. In some cases, organizations may [need to contact Chocolatey](https://chocolatey.org/contact/blocked-ip-address) directly to have access restored.

This guide will cover how to set up a feed to proxy packages from the Chocolatey Community Repository using ProGet. 

## Create a Chocolatey Proxy Feed

Start by selecting "Feeds" and "Create New Feed". Next, select "Chocolatey Packages", as we will creating a feed to proxy Chocolatey packages.

![Create Feed](/resources/docs/proget-chocolatey-newfeed.png){height="" width="50%"}

Now select "Free/Open Source Chocolatey Packages", which will allow us to proxy packages from the Chocolatey Community Repository.

![Select Feed](/resources/docs/proget-chocolatey-createconnector.png){height="" width="50%"}

From here, name your feed. In this example, we'll call ours `public-chocolatey`. Then select "Create New Feed".

![Name Feed](/resources/docs/proget-chocolatey-nameproxyfeed.png){height="" width="50%"}

ProGet will then redirect you to the newly created `public-chocolatey` feed, which will now be populated with packages proxied from the Chocolatey Community Repository.

![Proxy Feed](/resources/docs/proget-chocolatey-proxyfeed.png){height="" width="50%"}

## Additional Resources

You can visit [Chocolatey's Zendesk Help Center](https://chocolatey.zendesk.com/hc/en-us/articles/43231653511187-Configuring-Chocolatey-Sources-Inedo-ProGet) for more information on configuring Chocolatey sources for your ProGet instance.