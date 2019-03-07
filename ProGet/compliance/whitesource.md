---
title: Whitesource Integration
subtitle: Integrating ProGet with WhiteSource
sequence: 300
keywords: proget, vulnerabilities, license filtering
---

The [WhiteSource extension](/den/proget/whitesource) provides a Package Access Rule that will block packages with vulnerabilities, improper licenses, or that don't meet other conditions as defined in your WhiteSource account.

## Connecting to WhiteSource {#connecting data-title="Connecting to WhiteSource"}

Using the WhiteSource extension will allow you to combine vulnerability scanning and license filtering with your package management. The guide below will take you through using the integration. It's important to note that the guide was designed for ProGet users who are new to WhiteSource. If you are an existing WhiteSource user, and wish to apply your existing organization wide policies to your ProGet instance, skip to [The WhiteSource Extension in ProGet](#whitesource-in-proget), and apply your organization's API Key.

### Configuring WhiteSource {#configuring data-title="Configuring WhiteSource"}

In order to utilize this integration you must first request a trial key from WhiteSource. You can do this [here](https://www.whitesourcesoftware.com/trial3/).

Next, login to your WhiteSource instance. Once logged in, we recommend creating a Product corresponding to each feed in ProGet that you wish to interact with WhiteSource. In this example, we're adding a Product that corresponds to our NuGet *Default* feed in ProGet.

![](/resources/documentation/proget/whitesource/nuget-default.png)

![](/resources/documentation/proget/whitesource/create-new-product.png)

Once your corresponding Products are added, you can create Product-specific Policies.

### Creating Policies {#creating data-title="Creating Policies"}

Policies are rules that restrict access to specific packages based on license type and vulnerability severity. Policies can be set across an organization, or for an individual Product. If you wish to have the same Policies for each of your Feeds in ProGet, you can set Policies at the organizational level via the Policies tab.

It's important to note priority will resolve conflicting Polices, and Product-level Policies override Organizational (global) Policies.

To add Product-specific Policies, select the Product you just created: *Product > NuGet Default*

![](/resources/documentation/proget/whitesource/product-specific.png)

From here select: *Policies > Add Policy*

![](/resources/documentation/proget/whitesource/add-policy.png)

Here you'll notice there are many different types of Polices that can be configured in WhiteSource. The only types ProGet interacts with are Security Vulnerability Severity, and License Group. The only action you can use with ProGet is *Reject*.

For this example we'll add a Policy to *Reject* packages with high-severity vulnerabilities.

![](/resources/documentation/proget/whitesource/vulnerability-severity.png)

### Integrating WhiteSource and ProGet {#integrating data-title="Integrating WhiteSource and ProGet"}

Once you've added your necessary license-type and vulnerability-severity Policies in WhiteSource, navigate to the *Integrate* tab. Here you will see the various API tokens that are used to interact with other tools. For the WhiteSource and ProGet integration, we recommend using the corresponding Product API Token. If you are an existing WhiteSource user, and wish to apply your existing organization-wide Policies to your ProGet instance, follow the steps for creating a Package Access Rule, but apply your Organization's API Key.

![](/resources/documentation/proget/whitesource/product-tokens.png)

### The WhiteSource Extension in ProGet {#the data-title="The WhiteSource Extension in ProGet"}

The WhiteSource Integration is enabled through an extension. ProGet is shipped with this extension installed. You can navigate to the extensions tab in your instance to confirm the extension is installed. If missing from the installed extension list, you will also be able to install the extension from the [den](/den/all-items).

*Admin > Manage Extensions*

![](/resources/documentation/proget/whitesource/manage-extensions.png)

Once the extension is installed, Package Access Rules need to be configured at the feed-level for ProGet to ask WhiteSource if a package is allowed for download.

Navigate to the individual feed where you'd like to configure the Package Access Rule, as mentioned above, we'll use our NuGet Default feed.

*Feeds> NuGet Default > Manage Feed*

From here, we can add Package Access Rules by selecting WhiteSource as the provider.

![](/resources/documentation/proget/whitesource/package-access-rules.png)

![](/resources/documentation/proget/whitesource/create-package-access-rule.png)

Fill in the Name and Token field, the Name field can be whatever you'd like, but we recommend specifying it as the Product in WhiteSource.

![](/resources/documentation/proget/whitesource/configure-package-access-rule.png)

### Pull Packages {#pull data-title="Pull Packages"}

You can then use ProGet as normal. Some packages may be restricted from download based on the Policies you configured in WhiteSource.

We will try and download the NuGet Package pdfbox 1.1.1.

Here we can see that the download has been blocked.

![](/resources/documentation/proget/whitesource/download-blocked.png)

Since we know this package has a vulnerability, this is expected behavior.

![](/resources/documentation/proget/whitesource/high-vulnerability.png)

Our WhiteSource Policy to *Reject* packages with known high vulnerabilities has blocked this package from being download.
