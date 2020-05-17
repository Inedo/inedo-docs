---
title: SAML Authentication Overview
sequence: 30
keywords: authentication,saml
show-headings-in-nav: true
---

::: {.attention .technical}
SAML support is currently only available in [certain Inedo product versions](#supported-versions). It is still considered a preview feature, because we are still learning how to support it. But if you're willing to try it out, we will do our best to make sure it works in your environment.
:::

## Overview {#overview data-title="Overview"}

SAML-based single sign-on is an authentication mechanism that uses a third-party identity provider to verify the identity of user, and relay user metadata back into the Inedo product, effectively replacing the Inedo product login page, with that of the identity provider's, or removes it altogether if a user is already signed-in to the identity provider. 

## Supported Product Versions {#supported-versions data-title="Supported Inedo Products"}

SAML authentication is currently in beta, installable from the Inedo Hub. This functionality is available in the following prerelease product versions:

{.docs}
 - **ProGet** -v5.2.20 and later (Enterprise edition only)
 - **BuildMaster** - *coming soon*
 - **Otter** - *coming soon*

## Configuring SAML {#configuration data-title="Configuring SAML"}

### 1. Enable Prerelease Features (ProGet 5.2 Only)

If you are using ProGet 5.2, visit the *Administration* > *Advanced Settings* page to enable the `Web.PrereleaseFeaturesEnabled` setting.

### 2. Configure Identity Provider

In general, configuring an identity provider requires the following steps to enable SAML authentication:

{.docs}
 - Create an entity or application
 - Configure a SSO login callback/redirect URL in that application
 - Obtain SAML metadata XML file contents

Instructions for the following specific identity providers can be found here:

{.docs}
 - [Azure Active Directory](azure-ad)
 - [PingID](ping-id)

### 3. Configure Inedo Product

Once the identity provider is configured to support authentication in an Inedo product, the following properties must be configured on the *Administration* > *Enable Single Sign-on (SAML)* page:

{.docs}
 - **User directory** - configures an "external" user directory that enables a third-party identity provider to automatically create/update users in the Inedo product when they are authenticated. You will be asked to create one on first use, and the directory should be unique per identity provider and identity provider application. *Note: all authenticated users are configured to be administrators by default on initial creation*
 - **SAML issuer** - a name supplied by the identity provider, commonly referred to as an "application". Depending on what the identity provider expects, it could be a simple name, GUID, or URL
 - **Display name attribute** - the SAML attribute that represents the display name in the Inedo Product *(will display "logged in as" this value)*
 - **Email attribute** - the SAML attribute that represents the email address of the user *(this is not used by ProGet)*
 - **SAML metadata** - the contents of the [SAML metadata XML](https://en.wikipedia.org/wiki/SAML_Metadata) obtained from your identity provider. The contents of this file will attempt to be parsed in order to extract public key signing certificates *(used to validate the SAML responses)* and the SAML SSO endpoint that the Inedo product sign-in page will redirect to

Once these settings are saved, they will be validated and any errors will appear on the page. If successful, SAML authentication will be enabled, and selecting "Log In" from the user dropdown will present a sign-in button instead of username and password fields.

::: {.attention .technical}
**Troubleshooting note:**  Once enabled, there may be errors related to the web application restarting and/or cookies. Simply click the *Clear Authentication Cookies* button to resolve this once the site restarts. See the [troubleshooting](#troubleshooting) section if you get locked out.
:::

## Technical Limitations {#limitations data-title="Technical Limitations"}

{.docs}
 - SAML authentication requires the user of a web browser, which means all other means of authentication to an Inedo product would need to be done using API keys.
 - There is no way to "test" that SAML authentication is working other than by enabling it and trying it. If there are any issues, see the [locked out](#locked-out) troubleshooting section below.
 - At this time, role-based permissions are not automatically resolved from the identity provider, and must be configured within the Inedo product itself under the associated directory provider, and may require adding users to groups in the Inedo product for granular permissions

## Troubleshooting {#troubleshooting data-title="Troubleshooting"}

### Locked out: Resetting the built-in user directory and Admin account {#locked-out}

To reset the configured user directory to the *built-in* directory, and reset the *Admin* account to the password *Admin*, a server administrator must run the following command on the Inedo product's server:

<tab-block>
<tab name="ProGet">

```
(proget-installation-directory)\Service> .\ProGet.Service.exe resetadminpassword
```
</tab>
<tab name="BuildMaster">

```
(buildmaster-installation-directory)\Service> .\BuildMaster.Service.exe resetadminpassword
```
</tab>
<tab name="Otter">

```
(otter-installation-directory)\Service> .\Otter.Service.exe resetadminpassword
```
</tab>
</tab-block>

Once the user directory and Admin account are reset, the web application must be restarted. To restart the Integrated Web Server, run the following PowerShell command:

<tab-block>
<tab name="ProGet">

```
Restart-Service INEDOPROGETWEBSVC
```

</tab>
<tab name="BuildMaster">

```
Restart-Service INEDOBUILDMASTERWEBSVC
```

</tab>
<tab name="Otter">

```
Restart-Service INEDOOTTERWEBSVC
```
</tab>
</tab-block>

If your Inedo product is installed to run on IIS, use the *Restart-WebAppPool* command or recycle it directly in the IIS management UI. By default, the application pool is named ProGetAppPool, BuildMasterAppPool, or OtterAppPool, but may have been changed by a system administrator during installation.

The next time you visit after running these commands, there may be stale authentication cookies that can be cleared from the corresponding error page, (if they are automatically detected) or by simply clearing web browser cookies manually. When testing access, make sure to visit the root URL (for example: `https://proget-server/`).
