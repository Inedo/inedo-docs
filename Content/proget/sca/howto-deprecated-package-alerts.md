---
title: "HOWTO: Receive Alerts for Deprecated Packages in Use"
order: 5
---

ProGet 2025 will introduce [OSS Metadata Updating & Caching](/docs/proget/sca/policies#oss-metadata-updating-caching) for open-source packages you're using from public repositories, helping you ensure the packages you're using haven't been deprecated, unlisted, or are outdated. This is currently available in ProGet 2024.12 as a preview feature supporting NuGet and npm feeds.

Deprecated packages are those no longer recommended for use due to being replaced by newer versions, containing security vulnerabilities, or no longer being actively maintained. Continuing to use deprecated packages can lead to security risks and compatibility issues that can impact the stability and functionality of your software. 

Receiving alerts when a package becomes deprecated allows you to quickly address these issues, ensuring your application remains secure, reliable, and up-to-date.

This article will explain how to configure [policies](/docs/proget/sca/policies) for deprecated packages, and set up [alerts](https://docs.inedo.com/docs/proget/administration/proget-notifications-webhooks) for when deprecated packages are in use.

## Step 1: Enable OSS caching

Start by navigating to "Administration Overview" and selecting "OSS Metadata Updating & Caching".

![](/resources/docs/proget-admin-osscaching.jpg){height="" width="50%"}

Next select "Enable"

![](/resources/docs/proget-osscaching-enable.jpg){height="" width="50%"}

## Step 2: Configure Policies for Deprecated Packages

Return to "Administration Overview" and select "Package Policies & Rules".

![](/resources/docs/proget-admin-policies.jpg){height="" width="50%"}

Then select "edit" under the "Global" policy.

![](/resources/docs/proget-policies-edit.jpg){height="" width="50%"}

Next, select "edit" under "Other Rules".

![](/resources/docs/proget-policies-edit-other.jpg){height="" width="50%"}

From here, set "Deprecation Rule" as "Noncompliant" and select "Save".

![](/resources/docs/proget-policies-other-deprecated.jpg){height="" width="50%"}

## Step 3: Set Up Alerts

Return to the "Administration Overview" once more and select "Notifiers and Webhooks".

![](/resources/docs/proget-admin-notifiers.jpg){height="" width="50%"}

From here select "Create Notifier".

![](/resources/docs/proget-notifiers-create.jpg){height="" width="50%"}

Now set "Event Type" as "Package Status Changed", and set "Action" to your preferred notifier (email, Slack or Teams message, etc.), along with the relevant email or URL.

![](/resources/docs/proget-notifiers-new-slack.jpg){height="" width="50%"}

Finally, select "Save" to create this notifier, which will now be sent when a deprecated package is in use.

## Step 4: Optional: Customize Conditions & Messages

When creating the notifier in Step 3, you have the choice of setting some additional options:

### Conditions
On this tab you can set the notifier to run on specific feeds only, or for packages that match specific criteria.

![](/resources/docs/proget-notifiers-new-conditions.jpg){height="" width="50%"}

This includes using advanced conditions like triggering notifications only for packages with a vulnerability score above a certain threshold (e.g., $Compare($VulnerabilityScore,>=,7.5)) or based on patterns in package names (e.g., $MatchesRegex($PackageName, "^MyPackage\.*$")). These conditions give you more control over when notifications are sent.

### Custom Message
You can create a customized text to be sent, be it email, slack or teams message, or customized webhook:

![](/resources/docs/proget-notifiers-new-message.jpg){height="" width="50%"}

You can personalize these messages using [variables and expressions](/docs/proget/administration/proget-notifications-webhooks/proget-notifications-custom-webhook#variables-expressions). Your notifications can include dynamic content such as package details, user actions, or data from third-party APIs. Additionally, custom webhooks can be configured to integrate with external tools, enabling automated workflows or audits. 

For more details and examples, take a look at [Customizing Content & Webhooks and Variables & Expressions](/docs/proget/administration/proget-notifications-webhooks/proget-notifications-custom-webhook).
