---
title: "HOWTO: Connect to Teams"
order: 3
---

[Notifications & Webhooks](/docs/proget/administration/proget-notifications-webhooks) let you connect two tools to notify users, trigger automated workflows, or audit certain events. They can alert you via Teams when new vulnerabilities are detected, a noncompliant package is discovered, and when other package-related events occur.

In this page, we show you how to connect ProGet to Teams and sample payload messages to copy/paste into your own webhook.

:::(Info) (🔒 ProGet Enterprise Feature)
ProGet Teams Notifications are not available in ProGet Basic Edition, but custom webhooks are. See [License Restrictions](/docs/proget/administration/license) to learn more.
:::


## Step 1: Create a Webhook Workflow in Teams

::: (Warn) (ProGet 2024.0 to 2024.31)
Microsoft is in the process of [deprecating webhooks via connectors](https://learn.microsoft.com/en-us/microsoftteams/platform/webhooks-and-connectors/how-to/add-incoming-webhook?tabs=newteams%2Cdotnet) and replacing it with a [webhooks via workflows method](https://support.microsoft.com/en-us/office/create-incoming-webhooks-with-workflows-for-microsoft-teams-8ae491c7-0394-4861-ba59-055e33f75498).  

In ProGet 2024.0 to 2024.31, when creating a Teams notifier, ProGet leveraged the Teams' connectors for sending Webhooks.  To use the workflow method in these versions, you will need to create a Custom Webhook and use the [new webhook body](https://learn.microsoft.com/en-us/connectors/teams/?tabs=text1%2Cdotnet#microsoft-teams-webhook) for the notifier content.

Starting in ProGet 2024.32, existing teams notifiers will be considered custom webhooks and new Teams notifiers will leverage the new [webhooks via workflows method](https://support.microsoft.com/en-us/office/create-incoming-webhooks-with-workflows-for-microsoft-teams-8ae491c7-0394-4861-ba59-055e33f75498).  To convert these to use the new workflow method, you will need to delete your old Teams notifier and create new teams notifiers using the following guide.
:::

Microsoft Teams makes creating webhooks very easy. Inside Teams, navigate to the desired channel, click the three-dots button, select "Workflows," and then search for "`webhook`".

![teams-webhook-template-search.png](/resources/docs/TeamsWebHooksImages/teams-webhook-template-search.png){height="" width=""}

Select the "Post to a channel when a webhook request is received" and then name your webhook.

![teams-webhook-template-name.png](/resources/docs/TeamsWebHooksImages/teams-webhook-template-name.png){height="" width=""}

Select which Team and Channel you want notifications to be sent to.

![teams-webhook-template-channel.png](/resources/docs/TeamsWebHooksImages/teams-webhook-template-channel.png){height="" width=""}

Teams will create a URL that you must copy and bring it over to ProGet.

![teams-webhook-template-url.png](/resources/docs/TeamsWebHooksImages/teams-webhook-template-url.png){height="" width=""}

## Step 2: Create a Teams Message in ProGet

In your ProGet instance, navigating to "Administration" > "Notifiers and Webhooks" under "Global Components". 

![](/resources/docs/proget-administration-notifiers.png){height="" width="50%"}

From here select "Create Notifier".

![](/resources/docs/proget-notifier-create.png){height="" width="50%"}

Select the "Event" for the notifier, select "Teams Message", and then enter your Teams URL.

![](/resources/docs/proget-webhook-teams-new.png){height="" width="50%"}

You can also select the "Customize Teams" tab to add custom text to your Teams Message.

![](/resources/docs/proget-notifier-custom-teams.png){height="" width="50%"}

## Step 3: (Optional) Test your Notifier

After you've created the notification, take the configured action in ProGet, and your ProGet message should be sent to Teams.

## Additional Notes

Creating a global notifier requires selecting "always run / all feeds" for "Run on feed" under the "Conditions" tab, but for Docker feeds, you will not be able to select this due to different Docker URLs. You can also set it to run on specific feeds only by selecting the feed.

![](/resources/docs/proget-create-notifier-feed-teams.png){height="" width="50%"}

## ProGet 2023 and earlier
Notifications and Webhooks are available as a feature in ProGet 2024 as a replacement feature for [Webhooks (archive.org)](https://web.archive.org/web/20231210015731/https://docs.inedo.com/docs/proget-advanced-webhooks). The features are substantially similar.
