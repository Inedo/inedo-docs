---
title: PingID Single Sign-on
sequence: 200
keywords: authentication,saml,pingid
show-headings-in-nav: true
---

::: {.attention .technical}
SAML support is currently only available in prerelease versions of certain Inedo products. It is still considered a proof-of-concept, and may be removed at any time.
:::
## Overview

This documentation is specific to configuring SAML with PingID. Refer to the [SAML Authentication Overview](overview) for more information.

## Configuring PingID

### 1. Configure Application / Connection {#step-1 data-title="Configure Application"}

In the PingOne console, the *Connections* tab has a list of applications that will connect. To create one:

1. Click the `[+ Application]` button
2. Choose "Web App"
3. Choose connection type "SAML"
4. Use the Inedo product as the name i.e. `ProGet`
5. Add an *ACS URL* of: `https://{inedo-product-host}/saml-acs-callback` - **Note: PingID requires an HTTPS URL**
6. Specify *ENTITY ID* of whatever you want (i.e. `ProGet`), and this value will be used as the **SAML issuer** value
7. Leave defaults for everything else
8. Once created, **ensure the application is enabled** (they are disabled by default)

### 2. Map Attributes {#step-2 data-title="Map Attributes"}

An Inedo product can use any attributes for display name or email, but the following mappings will require no additional configuration on the Inedo product end:

| PingOne Attribute | Application Attribute |
|-|-|
| `Formatted` | `DisplayName` |
| `Email Address` | `Email` |

### 3. Obtain SAML Metadata {#step-3 data-title="Obtain SAML Metadata"}

It is easiest just to expand the *Configuration* sub-tab of an application, and locate the **IDP METADATA URL** text field, then browse to it.

Otherwise, the SAML metadata for PingID is found at the URL:

```
https://auth.pingone.com/{environment-id}/saml20/metadata/{application-id}
```

The value for `{environment-id}` is a GUID found in the PingOne Console *Settings* tab under **ENVIRONMENT ID**. The value for `{application-id}` is a GUID found in the *Connections* tab when the application is expanded under **CLIENT ID**.

### 4. Inedo Product Configuration {#step-4 data-title="Inedo Product Configuration"}

{.docs}
 - **SAML issuer** - use **ENTITY ID** from the app's *SAML Settings*
 - **Display name attribute** - `DisplayName`, or a custom application attribute
 - **Email attribute** - `Email`, or a custom application attribute
 - **SAML metadata** - copy/paste whole XML file contents from step 3

