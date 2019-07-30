---
title: API Access and API Keys
subtitle: API Access and API Keys
sequence: 120
keywords: buildmaster, api
---

An API key is used to grant programmatic access to BuildMaster, and allows scripts, tools, and other integrations to automate and interact with the system.

### Creating and Managing API Keys {#creating-keys}

To create or manage API keys, go to `Administration` > `API Keys and Access Logs`. You will be presented with a list of API keys in the system, as well as the ability to edit, delete, and create new ones.

The most important property of an API key is the **Key**. This is an arbitrary string that acts like a password, so make sure to treat it like one. You can set the key value to anything you'd like, or let BuildMaster auto-generate a value for you.

The **Description** field is used for a human-friendly name, and can be used to describe what the key is used for.

### API Key Permissions {#permissions}

A key can only be used for the API endpoints that you specify:

{.docs}
- [Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build)
- [Infrastructure Management API](/docs/buildmaster/reference/api/infrastructure)
- [Variables Management API](/docs/buildmaster/reference/api/variables)
- [CI Badge API](/docs/buildmaster/reference/api/ci-badge)

:::attention {.analogy}
Granting access to the [Native API](/docs/buildmaster/reference/api#native) will effectively allow for full control of the instance.
:::

### Logging Options

You can configure an API key to log both the request and the response body of each request. While this is really important for debugging purposes, if you have a lot of large requests, there will be a nontrivial overhead and amount of disk space required.

## Using API Keys {#usage}

An API key may be passed to any endpoint in one of four ways, depending on the content type of the expected request:

| Method | Name or Key | Details |
|----|----|----|
|Request header| `X-ApiKey` | *all content types* |
|Querystring value| `key` | *all content types* |
|Form value| `key` | only `applications/x-www-form-urlencoded` content type |
|JSON property | `API_Key` | on root object, only `application/json` content type |

### User Impersonation {#impersonation}

API keys may optionally be associated with a user account. Doing so allows for more granular task permissions to be applied to the specific API endpoints to which the key allows access. The supplied user name must match the exact user name of the account it is impersonating, for example: `kharnagy@domain.corp`

When a user name is omitted, the user is treated as an administrator (also known as the "API key user"), with full permissions to any of the permitted APIs as configured by the API key.

#### Windows Integrated Authentication and API Keys {#integrated-windows-auth}

If you've configured [Windows Integrated Authentication](/docs/various/ldap/integrated-authentication), the client will first need to authenticate with an Active Directory account, which may make API-key based authentication redundant.
