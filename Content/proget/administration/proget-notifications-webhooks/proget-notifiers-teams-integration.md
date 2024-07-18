---
title: "HOWTO: Connect to Teams"
order: 3
---

[Notifications & Webhooks](/docs/proget/administration/proget-notifications-webhooks) let you connect two tools to notify users, trigger automated workflows, or audit certain events. They can alert you via Teams when new vulnerabilities are detected, a noncompliant package is discovered, and when other package-related events occur.

In this page, we show you how to connect ProGet to Teams and sample payload messages to copy/paste into your own webhook.

:::(Info) (🔒 ProGet Enterprise Feature)
ProGet Teams Notifications are not available in ProGet Basic Edition, but custom webhooks are. See [License Restrictions](/docs/proget/administration/license) to learn more.
:::


## Step 1: Create a Webhook in Teams

Microsoft Teams makes creating webhooks very easy. Inside Teams, navigate to the desired channel, and click the three-dots button. Select "Connectors" and "Incoming Webhook."

![TeamsConnectorAdd.png](/resources/docs/TeamsConnectorAdd.png){height="" width=""}

Name your webhook, and optionally give it a custom image.

![TeamsConnectorConfig1.png](/resources/docs/TeamsConnectorConfig1.png){height="" width=""}

Teams will create a URL that you must copy and bring it over to ProGet.

![TeamsConnectorConfig2.png](/resources/docs/TeamsConnectorConfig2.png){height="" width=""}

## Step 2: Create a Teams Message in ProGet

In your ProGet instance, navigating to "Administration" > "Notifiers and Webhooks" under "Global Components". 

![](/resources/docs/proget-administration-notifiers.png){height="" width="50%"}

From here select "Create Notifier".

![](/resources/docs/proget-notifier-create.png){height="" width="50%"}

Select the "Event" for the notifier, select "Teams Message", and then enter your Slack URL.

![](/resources/docs/proget-webhook-teams-new.png){height="" width="50%"}

You can also select the "Customize Teams" tab to add custom text to your Teams Message.

![](/resources/docs/proget-notifier-custom-teams.png){height="" width="50%"}

## Step 3: (Optional) Test your Notifier

After you've created the notification, take the configured action in ProGet, and your ProGet message should be sent to Teams.

## Additional Notes

Creating a global notifier requires selecting "always run / all feeds" for "Run on feed" under the "Conditions" tab, but for Docker feeds, you will not be able to select this due to different Docker URLs. You can also set it to run on specific feeds only by selecting the feed:

![](/resources/docs/proget-create-notifier-feed-teams.png){height="" width="50%"}

## ProGet 2023 and earlier
Notifications and Webhooks are available as a feature in ProGet 2024 as a replacement feature for [Webhooks (archive.org)](https://web.archive.org/web/20231210015731/https://docs.inedo.com/docs/proget-advanced-webhooks). The features are substantially similar.
