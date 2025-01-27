---
title: "Troubleshooting LDAP/AD Integration"
order: 5
---

## Locked out: Restoring Default Admin Account
<a name="locked=out"></a>If you ever get "locked out" of an Inedo product, either due to misconfiguration or a lost password, you can restore the default `Admin`/`Admin` account and reenable Built-in User Sign-on. This involves:

1. Logging into the server that the product is installed on
2. Running the `resetadminpassword` command using the service executable
3. Restarting the service and application pool (if IIS hosted)
4. Closing open browser windows or clearing cookies
5. Navigate to root URL of product (e.g. `https://myproget.company.local/`)

#### HOWTO: Restore ProGet Default Admin Account
This Script will reset the account on Windows, so you can just copy/paste as needed.

```(PowerShell)
"%progamfiles%\ProGet\Service\ProGet.Service.exe resetadminpassword
try { Restart-WebAppPool ProGetAppPool } 
catch { Restart-Service INEDOPROGETWEBSVC } 
```
Note that the try/catch statement is there to make this work for both IIS Hosted and Integrated Web Server.

#### HOWTO: Restore ProGet Running in Docker's Default Admin Account
This script will reset the account on Docker (Linux) using `docker exec` and will need to be ran from the Docker host.  Please be sure to update `PROGET_CONTAINER_NAME` to the ProGet container's name.

```(shell)
docker exec -it PROGET_CONTAINER_NAME "/usr/local/proget/service/ProGet.Service resetadminpassword"
docker restart PROGET_CONTAINER_NAME
```

## The Active Directory (New) user directory is not found/not selectable 

The *Active Directory (New)* user directory is loaded from the InedoCore extension. Visit the extensions page (Administration &rsaquo; Extensions) to verify that the InedoCore extension is loaded on that page.

An instance of the user directory should be visible on the *Manage User Directories* page (Administration &rsaquo; Change User Directory &rsaquo; Advanced). If there isn't one (e.g. after a reset), click the *Add User Directory* button and select *Active Directory (New)*. Configure the user directory as detailed in the [Advanced Configuration](/docs/installation/security-ldap-active-directory/various-ldap-v3-advanced){target="_blank"} section, and then click Save. This will restore the user directory on the *Change User Directory* page.

## Privileges assigned to the Domain Users group not working 

The *Active Directory (New)* directory only considers a user's *direct* group membership, not indirect membership (i.e. a group's groups). This is done for performance reasons, as such queries are recursive in nature and may have severe performance issues (particularly with multiple domains).

We have had many support inquiries related to the assignment of privileges to the *Domain Users* group, and privileges not cascading to every user. We do not recommend using this group as a *catch-all* to grant or restrict any privileges. Refer to [Virtual Privilege Assignment](/docs/installation/security-ldap-active-directory/various-ldap-ldap-active-directory#virtual-privilege-assignment){target="_blank"} to configure *catch-all* privileges.

## Privileges assigned to a group are not working
The *Active Directory (New)* directory only considers a user's *direct* group membership, not indirect membership (i.e. a group's groups). This is done for performance reasons, as such queries are recursive in nature and may have severe performance issues (particularly with multiple domains). 

You can enable recursive group search in [Active Directory / LDAP v3](/docs/installation/security-ldap-active-directory/various-ldap-v3-advanced), but lots of recursive groups can cause performance issues and circular group dependencies will cause errors.  In v4, we have added support for a special Active Directory only query that allows these recursive groups to be queried much more efficiently. To configure this, see the [V4: Advanced Configuration](/docs/installation/security-ldap-active-directory/various-ldap-v4-advanced) documentation.

## Integrated Authentication Not Working

The underlying mechanisms for Integrated Windows Authentication are handled at the HTTP.SYS layer, which our products do not, nor cannot, access. Our code simply looks for a server variable named `LOGON_USER`, which is added by the web server (IIS or IIS components we use in the self-hosted version). When you enable Integrated Windows Authentication in our products, users will be automatically logged in if the `LOGON_USER` variable is present. 

Understanding this mechanism will help troubleshoot when it goes wrong, but we've also put together [some tips](/docs/installation/security-ldap-active-directory/various-ldap-troubleshooting#iwa-tips){target="_blank"} from our users about what they've found works.

### Behind The Scenes: Integrated Windows Authentication
Integrated Windows Authentication is a very complicated mechanism that requires a properly configured client, server, and domain. To summarize the most common IWA set-up ([Kerberos Authentication](https://technet.microsoft.com/en-us/library/cc772815(v=ws.10).aspx)):

- The web server sends a 401 (unauthorized) response with a specially encrypted HTTP response header
- The web browser decrypts the header, and sends another request with a special token indicating the user name as an HTTP request header
- The web server reads this header and contacts the domain controller to verify the client token
- The web server adds the `LOGON_USER` variable, strips out the special client token, and sends the modified request to the application software (ProGet, BuildMaster, or Otter) for processing

A lot of things can go wrong in this chain, and describing how to troubleshoot and set-up your network is well beyond the scope of this document and our support. But fortunately, since it’s not at all related to our products, there are a myriad of resources for helping debug this; just search *Integrated Windows Authentication not working*.

### TIP: Add BackConnectionHostNames for same-server or self connections

Integrated Windows Authentication often doesn't work when you're doing a same-server connection. For example: browsing to BuildMaster from a browser on your BuildMaster server, using a connector in ProGet to connect to itself, etc.

Setting the `BackConnectionHostNames` registry value may help!

1. In Registry Editor, locate and then click the following registry key: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\MSV1_0`
2. Right-click `MSV1_0`, point to New, and then click Multi-String Value.
3. Type `BackConnectionHostNames`, and then press ENTER.
4. Right-click `BackConnectionHostNames`, and then click Modify.
5. In the Value data box, type the host names for the sites that are on the local computer (e.g. `proget.kramerica.local`), one per line, and then click OK.
6. Restart machine

### TIP: Setup a Second site in IIS

Many clients will never support Integrated Windows Authentication, which means they will never be able to connect to your instance. To around this, you can create a second site in IIS that doesn't have Integrated Windows Authentication configured; this will require a different hostname and/or port.

### TIP: Configure a Service Principal Name

You may need to configure a [Service Principal Name](https://docs.microsoft.com/en-us/windows/win32/ad/service-principal-names); Windows is supposed to do this automatically for you when you set up a DNS name, but you may need to use the [`setspn` tool](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc731241(v%3Dws.11)).

## Integrated Authentication Not Working
See [Troubleshooting Integrated Authentication](/docs/installation/security-ldap-active-directory/various-ldap-integrated-authentication#troubleshooting)