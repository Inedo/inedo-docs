---
title: API Access and API Keys
subtitle: API Access and API keys
sequence: 120
keywords: proget,packages
show-headings-in-nav: true
---

An API Key is used to grant programmatic access to Otter, and allows scripts, tools, and other integrations to automate and interact with the system.

## Creating and Managing API Keys {#creating-keys data-title="Creating API Keys"}

To create or manage API Keys, go to Administration > API Keys and Access Logs. You will be presented with a list of API Keys in the system, as well as the ability to edit, delete, and create new ones.

The most important property of an API Key is the **Key**. This is an arbitrary string that acts like a password, so make sure to treat it like one. You can set the key value to anything you'd like, or let Otter auto-generate a value for you.

The **Description** field is used for a human-friendly name, and can be used to describe what the key is used for.

### API Key Permissions {#permissions data-title="API Key Permissions"}

A key can only be used for the API endpoints that you specify:

{.docs}
- Native API
- Job orchestration (Viewing and/or Triggering)
- Infrastructure management (Viewing and/or Updating
- Variables Management API
- Configuration Management API

:::attention {.analogy}
Granting access to the [Native API] will effectively allow for full control of the instance.
:::

#### Logging Options

You can configure an API key to log both the request and the response body of each request. While this is important for debugging purposes, if you have a lot of large requests, there will be a nontrivial overhead and amount of diskspace required.

## Using API Keys {#using data-title="Using API Keys"}

An API key may be passed to any endpoint (except Feed API endpoints - see below) in one of four ways, depending on the content type of the expected request:

{.docs}
- Request header (x-Apikey) - all content types
- Querystring value (key) - all content types
- Form value (key) - only applications/x-www-form-urlencoded content type
- JSON property (API_Key) on root object - only application/json content type

[Native API]: /support/documentation/otter/reference/api
