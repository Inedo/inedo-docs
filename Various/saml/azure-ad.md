---
title: Azure AD Single Sign-on
sequence: 100
keywords: authentication,saml,azure
show-headings-in-nav: true
---

## Overview

This documentation is specific to configuring SAML with Azure Active Directory. Refer to the [SAML Authentication Overview](overview) for more information.

## Configuring Azure AD {#azure-ad data-title="Configuring Azure AD"}

### 1. Obtain App Registration ID {#step-1 data-title="Register App"}

Before an Inedo product can authenticate with Azure, an App Registration must be created in Azure. When creating one, make sure to add a redirect URI of:

```
https://{inedo-product-host}/saml-acs-callback
```

Once created, the overview for the Azure App Registration will display a **Application (client) ID**, a GUID that should be used as the **SAML issuer** within the Inedo product configuration.

### 2. Obtain SAML Metadata {#step-2 data-title="Obtain SAML Metadata"}

The SAML metadata for Azure is found at the URL:

```
https://login.microsoftonline.com/{tenant-id}/FederationMetadata/2007-06/FederationMetadata.xml
```

The value for `{tenant-id}` is a GUID found in the App Registration under **Directory (tenant) ID**.

### 3. Inedo Product Configuration {#step-3 data-title="Inedo Product Configuration"}

{.docs}
 - **SAML issuer** - use **Application (client) ID** from App Registration in step 1
 - **Display name attribute** - `http://schemas.microsoft.com/identity/claims/displayname`
 - **Email attribute** - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
 - **SAML metadata** - copy/paste whole XML file contents from step 2

