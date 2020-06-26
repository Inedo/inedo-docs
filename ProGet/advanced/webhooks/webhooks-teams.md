---
title: Connecting to Teams
subtitle: Using a Webhook to Connect ProGet to Teams
sequence: 200
keywords: proget, webhooks, teams
---

[Webhooks](/docs/proget/advanced/webhooks) let you connect two tools to notify users, trigger automated workflows, or audit certain events. ProGet's webhooks are highly customizable: seven different "methods" (like "Post" or "Delete") and six different "events" (like "Pulled" or "Promoted") give you a possible 156 different notification alerts. 

In this page, we show you how to connect ProGet to Teams and sample payload messages to copy/paste into your own webhook.

## Configuring the Teams Webhook {#slack data-title="Configuring a Teams/ProGet Webhook"}

Microsoft Teams makes creating webhooks very easy. Inside Teams, navigate to the desired channel, and click the three-dots button. Select "Connectors" and "Incoming Webhook."

![](/resources/documentation/proget/TeamsConnectorAdd.png){.screenshot}

Name your webhook, and optionally give it a custom image.

![](/resources/documentation/proget/TeamsConnectorConfig1.png){.screenshot}

Teams will create a URL that you must copy and bring it over to ProGet.

![](/resources/documentation/proget/TeamsConnectorConfig2.png){.screenshot}

Inside **ProGet**, go to the Manage Feed page, click the "Webhooks" tab, and click "create." Note that you can create a webhook at the feed or at global level.

![](/resources/documentation/proget/SlackProGetWebhook.png){.screenshot}

Teams's message Json is more complex because they use [Cards](https://docs.microsoft.com/en-us/microsoftteams/platform/task-modules-and-cards/cards/cards-reference) and therefore can be tedious to configure. Visit [Microsoft's connectors documentation page]( https://docs.microsoft.com/en-us/outlook/actionable-messages/send-via-connectors) to get some sample syntax, or just test your Teams webhook by copy/pasting this sample syntax we created:

```
{
  "@context": "https://schema.org/extensions",
  "@type": "MessageCard",
  "themeColor": "025291",
  "title": "ProGet WebHooks - $FeedType Package $WebhookEvent to $FeedName",
  "text": "**feed:** $FeedName, **package:** $PackageId, **version:** $PackageVersion, **hash:** $PackageHash, **packageType:** $FeedType, **event:** $WebhookEvent, **user:** $UserName"
}
```

After you've saved your webhook, take the configured action in ProGet, and your ProGet message should be sent to Teams.

## Additional Notes

You can create webhooks from the individual feed pages or by going to Settings > Webhooks > Create Webhook. Creating a global webhook requires selecting "(all feeds)," but for Docker feeds, you will not be able to select this due to different Docker URLs.

Webhooks are configurable in all versions of ProGet, but they will not fire in free editions.