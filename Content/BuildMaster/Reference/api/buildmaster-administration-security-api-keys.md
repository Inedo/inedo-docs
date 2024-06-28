---
title: "API Access and API Keys"
order: 1
---

An API key is used to grant programmatic access to BuildMaster and allows scripts, tools, and other integrations to automate and interact with the system.

### Creating and Managing API Keys

To create or manage API keys, go to Administration > API Keys and Access Logs. You will get a list of API keys in the system and the ability to edit, delete, and create new keys.

The most important property of an API key is the **Key**. This is an arbitrary string that works like a password, so treat it as such. You can set the key value to any value or have BuildMaster automatically generate a value for you.

The **Description** field is used for a human friendly name and can be used to describe what the key is used for.

### API Key Permissions
You can select how you'd like the API key to be used. A key can only be used for the API endpoints that you specify:

- [Native API](/docs/buildmaster/reference/api/native) (i.e. SOAP & JSON APIs)
- [Application Management API](/docs/buildmaster/reference/api/buildmaster-appmanagement-api)
- [Release & Build Deployment API](/docs/buildmaster/reference/api/release-and-build)
- [Infrastructure Management API](/docs/buildmaster/reference/api/infrastructure)
- [Variables Management API](/docs/buildmaster/reference/api/variables)
- [CI Badge API](/docs/buildmaster/reference/api/buildmaster-ci-cd-continuous-integration-server-badges)
<br>

:::(info)
Granting access to the [Native API](/docs/buildmaster/reference/api#native) will effectively allow for full control of the instance.
:::

### Logging Options

You can configure an API key to log both the request and the response to each request. While this is important for debugging purposes, if you have a lot of large requests, a not insignificant amount of overhead and amount of disk space required.

## Using API Keys

An API key can be passed to each endpoint in one of four ways, depending on the content type of the expected request:

| Method | Name or Key | Details |
|----|----|----|
|Request header| `X-ApiKey` | *all content types* |
|Querystring value| `key` | *all content types* |
|Form value| `key` | only `applications/x-www-form-urlencoded` content type |
|JSON property | `API_Key` | on root object, only `application/json` content type |

### User Impersonation

API keys can optionally be associated with a user account. This allows more detailed task permissions to be applied to the specific API endpoints to which the key grants access. The specified user name must exactly match the user name of the account it impersonates, for example: `kharnagy@domain.corp`

If a user name is omitted, the user is treated as an administrator (also known as an "API key user"), with full permissions to any of the permitted APIs as configured by the API key.

#### Windows Integrated Authentication and API Keys

If you've configured [Windows Integrated Authentication](/docs/installation/security-ldap-active-directory/various-ldap-integrated-authentication), the client must first authenticate with an Active Directory account, which may make API key authentication unnecessary.