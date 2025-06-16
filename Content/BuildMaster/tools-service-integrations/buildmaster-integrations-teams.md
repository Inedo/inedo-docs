---
title: "Microsoft Teams"
order: 10
---


[Microsoft Teams](https://www.microsoft.com/en-us/microsoft-365/microsoft-teams/group-chat-software) is an enterprise communications platform developed by Microsoft that is part of the Microsoft 365 family of products. BuildMaster can be integrated with Teams by posting messages to Microsoft Teams channels via an Incoming Webhooks Connector built in to Teams.

In this page, we will show you how BuildMaster can post messages to Teams by using a `Post-Http` operation in OtterScript. See the [OtterScript example blow](#otter-script-example).

## Configuring the Teams Webhook 

Microsoft Teams makes creating webhooks very easy. Within Teams, navigate to the channel you want and click the button with the three dots. Select Connectors and Incoming Webhook.

![Add Connector](/resources/docs/buildmaster-connectors-teams-add.png){height="" width="50%"}

Name your webhook, optionally give it a custom image.

![Name Webhook](/resources/docs/buildmaster-connectors-teams-name.png){height="" width="50%"}

Teams will create a URL that you will need to copy and paste into your OtterScript.

![URL](/resources/docs/buildmaster-connectors-teams-url.png){height="" width="50%"}

In **BuildMaster**, locate the OtterScript plan, module, pre-deployment script, or post-deployment script you want to send the message from. Then, add a new `Post-Http` operation that will be sent to the above URL. For the `TextData` property, you will need to include the [Teams message JSON](#teams-message-json). Your JSON message can leverage BuildMaster's variables and variable functions in the message.  See the [OtterScript example](#otter-script-example) below.


### Example: OtterScript to Post a Message to Microsoft Teams 

This is an example `Post-Http` operation in OtterScript to send a Teams message to an Incoming Webhook Connector configured in Teams.

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

Teams' message JSON is more complex because they use [Cards](https://docs.microsoft.com/en-us/microsoftteams/platform/task-modules-and-cards/cards/cards-reference) and therefore can be tedious to configure. Visit [Microsoft's connectors documentation page]( https://docs.microsoft.com/en-us/outlook/actionable-messages/send-via-connectors) to get some sample syntax, or just test your Teams webhook by copy/pasting the following sample syntax:

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