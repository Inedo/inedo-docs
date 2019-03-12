---
title: API Access and API Keys
subtitle: API Access and API Keys
sequence: 120
keywords: proget, packages
---
An API Key is used to grant programmatic access to ProGet, and allows scripts, tools, and other integrations to automate and interact with the system.

#### Creating and Managing API Keys {#creating-keys}

To create or manage API Keys, go to Administration > API Keys and Access Logs. You will be presented with a list of API Keys in the system, as well as the ability to edit, delete, and create new ones.

The most important property of an API Key is the **Key**. This is an arbitrary string that acts like a password, so make sure to treat it like one. You can set the key value to anything you'd like, or let ProGet auto-generate a value for you.

The **Description** field is used for a human-friendly name, and can be used to describe what the key is used for.

#### API Key permissions {#permissions}

A key can only be used for the API endpoints that you specify:

{.docs}
- Native API
- Package Promotion API
- Webhooks API
- Feed API

:::attention {.analogy}
Granting access to the [Native API](/support/documentation/proget/reference/api#native) will effectively allow for full control of the instance.
:::

You can also specify a "Feed API Username". When configured, the Feed API key will effectively authenticate as the specified user and be restricted to whatever privileges that user has. If no Feed API user is specified, the Feed API key will have permission to perform any feed-scoped privilege.

#### Logging Options

You can configure an API key to log both the request and the response body of each request. While this is really important for debugging purposes, if you have a lot of large requests, there will be a nontrivial overhead and amount of diskspace required.

### Using API Keys

An API key may be passed to any endpoint (except Feed API endpoints - see below) in one of four ways, depending on the content type of the expected request:

{.docs}
- Request header (x-Apikey) - all content types
- Querystring value (key) - all content types
- Form value (key) - only applications/x-www-form-urlencoded content type
- JSON property (API_Key) on root object - only application/json content type

#### API Keys for Feeds

Although different third-party package formats (NuGet, npm, etc.) have different APIs, many of the clients support using an API key to authenticate some operations like publishing packages. You can simply supply the ProGet API Key, and ProGet will use that to authenticate the request.

However, most clients will not send the API key for operations like listing or pulling packages. In this case, ProGet will issue an authentication challenge, and the client will respond by prompting for a username and password. In this case, you can supply `api` for the username, and your API Key for the password.

Note that, if you've configured [Windows Integrated Authentication](/support/documentation/various/ldap/integrated-authentication), the client will first need to authenticate with an Active Directory account, which may make API-key based authentication redundant.
