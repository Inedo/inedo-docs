---
title: Microsoft Teams
subtitle: Using Otter Script to Connect BuildMaster to Teams
sequence: 800
keywords: buildmaster, teams
---

[Microsoft Teams](https://www.microsoft.com/en-us/microsoft-365/microsoft-teams/group-chat-software) is a business communication platform developed by Microsoft, as part of the Microsoft 365 family of products. BuildMaster can integrate with Teams by POSTing messages to Microsoft Teams' channels using an Incoming Webhooks Connector built in to Teams.

In this page, we will show you how BuildMaster can post messages to Teams by using a `Post-Http` operation in Otter Script. See the [Otter Script example blow](#otter-script-example).

## Configuring the Teams Webhook {#slack data-title="Configuring an Incoming Teams Webhook"}

Microsoft Teams makes creating webhooks very easy. Inside Teams, navigate to the desired channel, and click the three-dots button. Select "Connectors" and "Incoming Webhook."

![](/resources/documentation/buildmaster/Teams/TeamsConnectorAdd.png){.screenshot}

Name your webhook, and optionally give it a custom image.

![](/resources/documentation/buildmaster/Teams/TeamsConnectorConfig.png){.screenshot}

Teams will create a URL that you must copy and bring it over to your Otter Script.

![](/resources/documentation/buildmaster/Teams/TeamsConnectorConfig2.png){.screenshot}

Inside **BuildMaster**, find the Otter Script plan, module, pre-deployment script, or post-deployment script you would like to send the message from. You will then add a new `Post-Http` operation that will post to the URL from above.  For the `TextData` property, you will need to include the [Teams message JSON](#teams-message-json).  Your JSON message can leverage BuildMaster's variables and variable functions in the message.  See the [Otter Script example](#otter-script-example) below.


### Example: Otter Script to POST a Message to Microsoft Teams {#otter-script-example}

This is an example `Post-Http` operation in Otter Script to send a Teams message to an Incoming Webhook Connector configured in Teams.

```
Post-Http <URL from Teams WebHook Configuration>
(
    ContentType: application/json,
    TextData: >>{
  "@context": "https://schema.org/extensions",
  "@type": "MessageCard",
  "themeColor": "F6A52B",
  "title": "BuildMaster - New Build $ApplicationName $ReleaseNumber.$BuildNumber",
  "text": "**New Build** $ReleaseNumber.$BuildNumber has completed.  View it at [Build $BuildNumber]($BuildMasterUrl(release))."}>>
);
```

### Microsoft Teams Message JSON {#teams-message-json}

Teams' message JSON is more complex because they use [Cards](https://docs.microsoft.com/en-us/microsoftteams/platform/task-modules-and-cards/cards/cards-reference) and therefore can be tedious to configure. Visit [Microsoft's connectors documentation page]( https://docs.microsoft.com/en-us/outlook/actionable-messages/send-via-connectors) to get some sample syntax, or just test your Teams webhook by copy/pasting this sample syntax we created:

```
{
  "@context": "https://schema.org/extensions",
  "@type": "MessageCard",
  "themeColor": "F6A52B",
  "title": "BuildMaster - New Build $ApplicationName $ReleaseNumber.$BuildNumber",
  "text": "**New Build** $ReleaseNumber.$BuildNumber has completed. View it at [Build $BuildNumber]($BuildMasterUrl(release))."
}
```

_Note:_ The `$BuildMasterUrl` function is only available in BuildMaster 6.2.21+. Check your functions reference inside of BuildMaster for more information.
