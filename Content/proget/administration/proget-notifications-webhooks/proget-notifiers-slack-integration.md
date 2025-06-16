---
title: "HOWTO: Connect to Slack"
order: 2
---

[Notifications & Webhooks](/docs/proget/administration/proget-notifications-webhooks) let you connect two tools to notify users, trigger automated workflows, or audit certain events. They can alert you via Slack when new vulnerabilities are detected, a noncompliant package is discovered, and when other package-related events occur.

In this page, we show you how to connect ProGet to Slack and sample payload messages to copy/paste into your own webhook.

:::(Info) (🔒 ProGet Enterprise Feature)
ProGet Slack Notifications are not available in ProGet Basic Edition, but custom webhooks are. See [License Restrictions](/docs/proget/administration/license) to learn more.
:::

## Step 1: Create a Webhook in Slack

To connect to Slack, start by following [this Slack tutorial](https://api.slack.com/messaging/webhooks) to create your incoming webhook application inside Slack. Slack now recommends this as the best way to add webhooks (instead of the previously recommended third-party app called Incoming Webhooks).

![Slack Webhooks](/resources/docs/proget-notifiers-slack-webhooks.png){height="" width="50%"}

Once you've created your Incoming Webhook application in Slack, you can optionally change the notification colors and the app's icon.

![Slack App](/resources/docs/proget-notifiers-slack-appicon.png){height="" width="50%"}

Note that to edit the webhook again, you must go to "Your Apps" inside Slack, rather than from the channel where the webhook posts.

## Step 2: Create a Slack Message in ProGet

In your ProGet instance, navigating to "Administration" > "Notifiers and Webhooks" under "Global Components". 

![Notifiers and Webhooks](/resources/docs/proget-administration-notifiers.png){height="" width="50%"}

From here select "Create Notifier".

![Create Notifier](/resources/docs/proget-notifier-create.png){height="" width="50%"}

Select the "Event" for the notifier, select "Slack Message", and then enter your Slack URL.

![New Notifier](/resources/docs/proget-webhook-slack-new.png){height="" width="50%"}

You can also select the "Customize Slack" tab to add custom text to your Slack Message.

![Custom Text](/resources/docs/proget-notifier-custom-slack.png){height="" width="50%"}

## Step 3: (Optional) Test your Slack Message

After you've created your notifier, take the configured action in ProGet, and your Slack should receive a notification from ProGet!

![Message Evolution](/resources/docs/SlackMessageEvolution.png){height="" width="50%"}

## Additional Notes

Creating a global notifier requires selecting "always run / all feeds" for "Run on feed" under the "Conditions" tab, but for Docker feeds, you will not be able to select this due to different Docker URLs. You can also set it to run on specific feeds only by selecting the feed.

![](/resources/docs/proget-create-notifier-feed-slack.png){height="" width="50%"}

## ProGet 2023 and earlier
Notifications and Webhooks are available as a feature in ProGet 2024 as a replacement feature for [Webhooks (archive.org)](https://web.archive.org/web/20231210015731/https://docs.inedo.com/docs/proget-advanced-webhooks). The features are substantially similar.