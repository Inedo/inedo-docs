---
title: "SAML Authentication Overview"
order: 1
---

:::(Error) 
SAML support is currently only available in [certain Inedo product versions](/docs/installation/saml-authentication/various-saml-overview#supported-versions){target="_blank"}.
:::

## Overview

SAML-based single sign-on is an authentication mechanism that uses a third-party identity provider to verify the identity of user, and relay user metadata back into the Inedo product, effectively replacing the Inedo product login page, with that of the identity provider's, or removes it altogether if a user is already signed-in to the identity provider. 

## Supported Product Versions

SAML authentication is is available in the following product versions:

 - **ProGet Enterprise** -v5.3 and later (preview feature available in v5.2.20+)
 - **BuildMaster Enterprise** - v2022 and later (not yet available as preview)
 - **Otter Enterprise** - v2022 and later (not yet available as preview)

## Configuring SAML

### 1. Enable Prerelease Features (ProGet 5.2 Only)

If you are using ProGet 5.2, visit the *Administration* > *Advanced Settings* page to enable the `Web.PrereleaseFeaturesEnabled` setting.

### 2. Configure Identity Provider

In general, configuring an identity provider requires the following steps to enable SAML authentication:

 - Create an entity or application
 - Configure a SSO login callback/redirect URL in that application to `https://«PROGET_HOST»/saml-acs-callback`
 - Obtain SAML metadata XML file contents

Instructions for the following specific identity providers can be found here:

 - [Azure Active Directory](/docs/installation/saml-authentication/various-saml-azure-ad){target="_blank"}
 - [PingID](/docs/installation/saml-authentication/various-saml-ping-id){target="_blank"}

### 3. Configure Inedo Product

Once the identity provider is configured to support authentication in an Inedo product, the following properties must be configured on the *Administration* > *Enable Single Sign-on (SAML)* page:

 - **SAML issuer** - a name supplied by the identity provider, commonly referred to as an "application". Depending on what the identity provider expects, it could be a simple name, GUID, or URL
 - **Display name attribute** - the SAML attribute that represents the display name in the Inedo Product *(will display "logged in as" this value)*
 - **Email attribute** - the SAML attribute that represents the email address of the user *(this is not used by ProGet)*
 - **Groups attribute** - the SAML attribute that represents the the user's groups. [See SAML Groups](#saml-groups)
 - **SAML metadata** - the contents of the [SAML metadata XML](https://en.wikipedia.org/wiki/SAML_Metadata) obtained from your identity provider. The contents of this file will attempt to be parsed in order to extract public key signing certificates *(used to validate the SAML responses)* and the SAML SSO endpoint that the Inedo product sign-in page will redirect to

Once these settings are saved, they will be validated and any errors will appear on the page. If successful, SAML authentication will be enabled, and selecting "Log In" from the user dropdown will present a sign-in button instead of username and password fields.

:::(Error) (**Troubleshooting note:** )
 Once enabled, there may be errors related to the web application restarting and/or cookies. Simply click the *Clear Authentication Cookies* button to resolve this once the site restarts. See the [troubleshooting](#troubleshooting) section if you get locked out.
:::


## SAML Groups 

**This is only supported in ProGet 2024.6 and above.**  ProGet can use the SAML groups claim to match groups to existing ProGet groups for authorization of secured tasks.  Group mapping is disabled by default, you will have to enable group mapping when configuring your SAML provider.  This can be enabled by:
      1.  Navigate to Administration > Manage Security page
      2. Click "change" to the right of SAML.
      3. Check the checkbox labeled "Map SAML groups to ProGet groups" and then click Save

The SAML groups claim are traditionally sent using either the `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` or the `groups` attribute, which passes a list of groups as strings.  This attribute can be customized on the SAML Configuration page to match the claim attribute passed from your SAML provider.  ProGet will use the names passed in the groups collection to match to existing groups in ProGet's Built-In User Directory and will auto-assign these groups to the generated Built-In user.  Please note that some SAML providers pass the Group ID instead of the Group Name by default, you must configure your SAML provider to pass the group names for matching to work.

::: (Info) (Note:)
If you are using [SAML in conjunction with an Active Directory User Directory](/docs/installation/saml-authentication/various-saml-azure-ad#connecting-azure-ad-mircosoft-entra-id-to-your-ad-user-directory), SAML Groups will always be ignored when matched to an AD user.  ProGet will only use the groups associated with the Active Directory/LDAP user.
:::

### Entra ID (Azure AD) Configuration
By default Entra ID will only pass the Group ID instead of the Group Name. This may make it difficult to manage your groups in ProGet.  Some extra steps are needed to configuring the SAML provider to pass Group Names, but the configuration will be based on your Entra ID setup.  Here are some common configurations:

**Entra ID is configured Microsoft Entra Connect Sync to synchronize your on-premise Active Directory to Entra ID**: You will need to use the `sAMAccountName` option for the group names.

**Entra ID is only using cloud-only groups:** 
1. Add any groups you want to send to ProGet to the Enterprise Application itself by navigating to Enterprise Applications -> You ProGet Application -> Users And Groups -> + Add -> Select the group(s) you want to add.
2. Configure your groups claim to use "Groups assigned to the application".  This will only sends groups that exist on the user and are assigned to the enterprise application.
3. Select "Cloud-only group display names" for the source attribute.

**Entra-ID is configured to use both cloud-only groups and groups synced from AD using Microsoft Entra Connect Sync:**
1. Add any groups you want to send to ProGet to the Enterprise Application itself by navigating to Enterprise Applications -> You ProGet Application -> Users And Groups -> + Add -> Select the group(s) you want to add.
2. Configure your groups claim to use "Groups assigned to the application".  This will only sends groups that exist on the user and are assigned to the enterprise application.
3. Select "sAMAccountName" for the source attribute and check the box "Emit group name for cloud-only groups".

For other configurations, see [Microsoft's Entra ID documentation for more information](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/how-to-connect-fed-group-claims).

## Technical Limitations

 - SAML authentication requires the user of a web browser, which means all other means of authentication to an Inedo product would need to be done using API keys.
 - There is no way to "test" that SAML authentication is working other than by enabling it and trying it. If there are any issues, see the [locked out](#locked-out) troubleshooting section below.
 - At this time, role-based permissions are not automatically resolved from the identity provider, and must be configured within the Inedo product itself under the associated directory provider, and may require adding users to groups in the Inedo product for granular permissions

## Troubleshooting 
### Locked out: Resetting the built-in user directory and Admin account

To reset the configured user directory to the *built-in* directory, and reset the *Admin* account to the password *Admin*, a server administrator must run the following command on the Inedo product's server:

#### ProGet
`(proget-installation-directory)\Service> .\ProGet.Service.exe resetadminpassword`

#### BuildMaster
`(buildmaster-installation-directory)\Service> .\BuildMaster.Service.exe resetadminpassword`

#### Otter
`(otter-installation-directory)\Service> .\Otter.Service.exe resetadminpassword`

Once the user directory and Admin account are reset, the web application must be restarted. To restart the Integrated Web Server, run the following PowerShell command:

#### ProGet
`Restart-Service INEDOPROGETWEBSVC`

#### BuildMaster
`Restart-Service INEDOBUILDMASTERWEBSVC`

#### Otter
`Restart-Service INEDOOTTERWEBSVC`

If your Inedo product is installed to run on IIS, use the *Restart-WebAppPool* command or recycle it directly in the IIS management UI. By default, the application pool is named ProGetAppPool, BuildMasterAppPool, or OtterAppPool, but may have been changed by a system administrator during installation.

The next time you visit after running these commands, there may be stale authentication cookies that can be cleared from the corresponding error page, (if they are automatically detected) or by simply clearing web browser cookies manually. When testing access, make sure to visit the root URL (for example: `https://proget-server/`).
