---
title: "Azure AD (Mircosoft Entra ID) Single Sign-on"
order: 2
---

## Overview

This documentation is specific to configuring SAML with Azure Active Directory (Mircosoft Entra ID). Refer to the [SAML Authentication Overview](/docs/installation/saml-authentication/various-saml-overview){target="_blank"} for more information.

## Configuring Azure AD (Mircosoft Entra ID)

Before you can use Azure AD (Mircosoft Entra ID) in Inedo's products, you will first need to create an Enterprise Application in Azure.

1. Navigate to Enterprise Applications and click "New application"
2. Click "Create your own application"
   1. Enter a name (e.g. ProGet SAML)
   2. Select "Integrate any other application you don't find in the gallery (Non-gallery)"
3. On the overview page, make note of the "Application ID", you will need this in the Inedo product configuration
4. Click "Users and groups" on the left and add your users to this application in Azure
5. Click "Single sign-on" on the left
   1. Select "SAML"
   2. Click "Edit" in the "Basic SAML Configuration" block
      1. Click "Add Identifier" and enter an identifier (e.g proget-saml)
      2. Click "Add reply URL" and enter "http://{inedo-product-host}/saml-acs-callback"
      3. Click "Save"
6. In the "SAML Signing Certificate" block, copy the "App Federation Metadata URL", this will be used in the Inedo product configuration

## Inedo Product Configuration

### Option 1: Configure using the Azure AD (Mircosoft Entra ID) provider
::: (INFO) (Note)
Currently this is only supported in ProGet v6.0.11+ when v6.1 preview security features are enabled.
:::

This is the simplest method for configuring Inedo Products to use Azure AD for SAML based single sign-on.

1. Navigate to _Administration -> Manage Security_
2. In the "Login Options" box, click "change" to the right of SAML
3. Select "Azure AD SAML Provider"
4. Create your [Enterprise Application](#configuring-azure-ad), if not already created
5. Click "Next"
6. Enter your Enterprise Application's "Application ID"
    1. This is commonly found on the Enterprise Application's Overview page
7. Enter your "Federation Metadata URL" 
    1. This is commonly found on the Enterprise Application's Single Sign-On page in the SAML Signing Certificate block
8. Click Save

### Option 2: Configuring using a Custom Provider
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
1. In the SAML Issuer, enter the Enterprise Application's Application ID
    1. This is commonly found on the Enterprise Application's Overview page
2. In a web browser, navigate to the Federation Metadata URL and copy the XML output
    1. This is commonly found on the Enterprise Application's Single Sign-On page in the SAML Signing Certificate block
3. In SAML Metadata, paste the XML from your federation metadata URL
4. For the Display name attribute, enter `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
5. For the Email attribute, enter `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
6. Click "Save"

## Connecting Azure AD (Mircosoft Entra ID) to Your AD User Directory

While SAML authentication does require the Built-In User Directory to be enabled, you can update SAML claims to allow you to match to [LDAP/AD User Directory](/docs/installation/security-ldap-active-directory) users.  If a match is found, it will use the permissions of hte LDAP/AD user.  If a user is not found, it will create a new user in the Built-In User Directory.

The claim that is used to match your username is the `nameidentifier` claim. A common way to map this to a User Directory user is to use the `ExtractMailPrefix()` transform on the `user.userprincipalname` attribute.  
![SAML-ClaimsTransform](/resources/docs/SAML-ClaimsTransform.png){height="" width="75%"}

There are some cases where using just the User Name is not enough.  The most common use cases are:
1. The user name already exists in the Built-In User Directory
2. You are using a multi-domain Active Directory Forest

In these cases, you will need to also include the domain suffix.  One way this can be done is by using the `Join()` transform.  This requires the on-premise domain information for User Name and DNS Domain Name to exist in your Azure AD (Microsoft Entra ID).
![SAML-JoinTransform](/resources/docs/SAML-JoinTransform.png){height="" width="50%"}