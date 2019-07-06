---
title: Troubleshooting
sequence: 40
keywords: ldap,active-directory
show-headings-in-nav: true
---

## Locked out: Resetting the built-in user directory and Admin account {#locked-out data-title="Locked Out"}

To reset the configured user directory to the *built-in* directory, and reset the *Admin* account to the password *Admin*, a server administrator must run the following command on the Inedo product's server:

For ProGet:

```
(proget-installation-directory)\Service> .\ProGet.Service.exe resetadminpassword
```

For BuildMaster:

```
(buildmaster-installation-directory)\Service> .\BuildMaster.Service.exe resetadminpassword
```

For Otter:

```
(otter-installation-directory)\Service> .\Otter.Service.exe resetadminpassword
```

Once the user directory and Admin account are reset, the web application must be restarted. To restart the Integrated Web Server, run the following PowerShell command:

For ProGet:

```
Restart-Service INEDOPROGETWEBSVC
```

For BuildMaster:

```
Restart-Service INEDOBUILDMASTERWEBSVC
```

For Otter:

```
Restart-Service INEDOOTTERWEBSVC
```

If your Inedo product is installed to run on IIS, use the *Restart-WebAppPool* command or recycle it directly in the IIS management UI. By default, the application pool is named ProGetAppPool, BuildMasterAppPool, or OtterAppPool, but may have been changed by a system administrator during installation.

The next time you visit after running these commands, there may be stale authentication cookies that can be cleared from the corresponding error page, (if they are automatically detected) or by simply clearing web browser cookies manually. When testing access, make sure to visit the root URL (for example: `https://proget-server/`).

## The Active Directory (New) user directory is not found/not selectable {#active-directory-new data-title="User Directory Not Found"}

The *Active Directory (New)* user directory is loaded from the InedoCore extension. Visit the extensions page (Administration &rsaquo; Extensions) to verify that the InedoCore extension is loaded on that page.

An instance of the user directory should be visible on the *Manage User Directories* page (Administration &rsaquo; Change User Directory &rsaquo; Advanced). If there isn't one (e.g. after a reset), click the *Add User Directory* button and select *Active Directory (New)*. Configure the user directory as detailed in the [Advanced Configuration](/support/documentation/various/ldap/advanced) section, and then click Save. This will restore the user directory on the *Change User Directory* page.

## Privileges assigned to the Domain Users group not working {#privileges-not-working data-title="Group-based Privileges Not Working"}

The *Active Directory (New)* directory only considers a user's *direct* group membership, not indirect membership (i.e. a group's groups). This is done for performance reasons, as such queries are recursive in nature and may have severe performance issues (particularly with multiple domains).

We have had many support inquiries related to the assignment of privileges to the *Domain Users* group, and privileges not cascading to every user. We do not recommend using this group as a *catch-all* to grant or restrict any privileges. Refer to [Virtual Privilege Assignment](ldap-active-directory#virtual-privilege-assignment) to configure *catch-all* privileges.

## Integrated Authentication Not Working {#authentication-not-working data-title="Integrated Authentication Not Working"}

The underlying mechanisms for this are handled at the HTTP.SYS layer, which our products do not, nor cannot, access. They simply looks for a server variable named `LOGON_USER`, added by the web server, and uses that if available. The only configuration change you can make is to look for that variable, or not; if the variable does not exist, then the issue lies outside of our products.

Integrated Windows Authentication is a very complicated mechanism that requires a properly configured client, server, and domain. To summarize the most common IWA set-up ([Kerberos Authentication](https://technet.microsoft.com/en-us/library/cc772815(v=ws.10).aspx)):

{.docs}
- The web server sends a 401 (unauthorized) response with a specially encrypted HTTP response header
- The web browser decrypts the header, and sends another request with a special token indicating the user name as a HTTP request header
- The web server reads this header and contacts the domain controller to verify the client token
- The web server adds the `LOGON_USER` variable, strips out the special client token, and sends the modified request to the application software (ProGet, BuildMaster, or Otter) for processing

A lot of things can go wrong in this chain, and describing how to troubleshoot and set-up your network is well beyond the scope of this document and our support. But fortunately, since it’s not at all related to our products, there are a myriad of resources for helping debug this; just search *Integrated Windows Authentication not working*.
