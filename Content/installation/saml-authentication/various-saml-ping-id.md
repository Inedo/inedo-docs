---
title: "PingID Single Sign-on"
order: 3
---

## Overview
This documentation is specific to configuring SAML with PingID. Refer to the [SAML Authentication Overview](/docs/installation/saml-authentication/various-saml-overview){target="_blank"} for more information.

## Configuring PingID

In the PingOne console, the Connections tab has a list of applications that will connect. To create one:
1. Click the `[+ Application]` button
2. Choose "Web App"
3. Choose connection type "SAML"
4. Use the Inedo product as the name i.e. **ProGet**
5. Add an ACS URL of: `https://{inedo-product-host}/saml-acs-callback` - **Note: PingID requires an HTTPS URL**
6. Specify ENTITY ID of whatever you want (i.e. **ProGet**), and this value will be used as the **SAML issuer** value
7. Leave defaults for everything else
8. Once created, **ensure the application is enabled** (they are disabled by default)

## Inedo Product Configuration

### Option 1: Configure using the PingID provider
::: (INFO) (Note)
Currently this is only supported in ProGet v6.0.11+ when v6.1 preview security features are enabled.
:::

This is the simplest method for configuring Inedo Products to use PingID for SAML based single sign-on.

1. Navigate to _Administration -> Manage Security_
2. In the "Login Options" box, click "change" to the right of SAML
3. Select "PingID SAML Provider"
4. Create your [ Application](#configuring-pingid), if not already created
5. Click "Next"
6. Enter your "Entity ID" from the PingID application's SAML Settings
7. Enter your "IDP  Metadata URL" from your PingID appication's *Configuration* sub-tab
8. Click Save 

### Option 2: Configure using a Custom Provider

::: (INFO) (Note)
This is the only supported way in ProGet 5.3 or using 6.0 security features in ProGet 6.0.
:::

When using v6.1 preview features:
1. Navigate to _Administration -> Manage Security_
2. In the "Login Options" box, click "change" to the right of SAML
3. Select "Custom/Other SAML Provider"

When using ProGet 5.3 or 6.0 security features:
1. Navigate to _Administration -> Enable Single Sign-on (SAML)_

#### Configuration Settings
1. In the SAML Issuer, enter the "Entity ID" from the PingID application's SAML Settings
2. In a web browser, navigate to the "IDP  Metadata URL" from your PingID appication's *Configuration* sub-tab
3. In SAML Metadata, paste the XML from your federation metadata URL
4. For the Display name attribute, enter `Formatted`
5. For the Email attribute, enter `Email Address`
6. Click "Save"

## Troubleshooting

### I cannot find my IDP Metadata URL
It is easiest just to expand the *Configuration* sub-tab of an application, and locate the **IDP METADATA URL** text field.

Otherwise, the SAML metadata for PingID is found at the URL:

`https://auth.pingone.com/{environment-id}/saml20/metadata/{application-id}`

The value for `{environment-id}` is a GUID found in the PingOne Console *Settings* tab under `ENVIRONMENT ID`. The value for `{application-id}` is a GUID found in the *Connections* tab when the application is expanded under **CLIENT ID**.

