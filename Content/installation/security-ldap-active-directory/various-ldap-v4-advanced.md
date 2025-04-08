---
title: "Active Directory/LDAP (v4)"
order: 2
---


To mitigate the risk of authentication errors after we make major changes to our Active Directory / LDAP integration code, we create a new "user directory" type that can be configured side-by-side with the existing types. This article documents the advanced configuration of the **Active Directory / LDAP (v4)** user directory, which is available in 2023 versions.

In most installations, the default configuration for the *Active Directory v4 (New)* user directory will *just work*; usually this is the case when the product's server is already joined to the domain associated with the users of the software. There are other cases when a more advanced configuration is required, common examples include:

- Your Inedo product is not installed on the domain, or users are located in a different domain altogether
- NETBIOS mappings cannot be resolved correctly
- Trust relationships with domains that have common/shared user names
- External domain authentication (hosted LDAP)
- You would like to enable LDAPS support
- Your users use nested groups

## Configuring your User Directory
For advanced configuration, navigate to Administration > Manage Security and click "edit" to the right of "Active Directory v4 (New)".  If you do not see this user directory, you will need to add a new "V4: Active Directory/LDAP (Preview)" directory.

::: (Info) (Note:)
Users and groups configured in tasks and permissions are compatible with both v3 and v4, allowing easy switch-over between user directories.
:::

### General
- **Name**: A simple name to identify the user directory in the UI
- **Domain**: The domain suffix to use for LDAP queries (ex: `kramerica.local`).  *If not specified, it will use the current domain your server is joined to.*
- **User Name**: The user name to use when connecting to your LDAP server.  This will need to be in the format of `domain\username` or `username@domain.local`. *If not specified, it will use the trust with the current domain your server is joined to.*
- **Password**: The password to use when connecting to your LDAP server.  *If not specified, it will use the trust with the current domain your server is joined to.*

### Connection
- **Domain Controller Host**: The host name or IP address of the domain controller or LDAP server. When specified, LDAP search queries will use this as the root of the search query (e.g. `LDAP://kramerica.local/`).  If not specified, it will use value is specified in the "Domain" field on the general tab or the LDAP root (i.e. `LDAP://`) if neither are specified.
- **LDAP Connection**: The connection protocol to connect to the LDAP server with.  The following values can be selected:
   - **Use LDAP** (default): Connects via the standard unencrypted LDAP protocol
   - **Use LDAPS**: Connects via LDAPS (LDAP over TLS/SSL) protocol
   - **Use LDAPS and bypass certificate errors**: Connects via LDAPS (LDAP over TLS/SSL) protocol but ignores any certificate warnings.  LDAP servers typically use domain signed certificates that not all servers, like our Docker images, trust by default.  This allows you to still connect via LDAPS and ignore the certificate error.
- **LDAP Port Override**: This overrides the port used to connect to the LDAP server with.  By default LDAP use 389 and LDAPS uses 636.
 
 ### LDAP Overrides
 LDAP overrides are not typically needed, but can be helpful when trying to filter down large Active Directories, using LDAP servers that differ from Active Directory, or when your server uses properties that differ from the defaults for users.
 
 - **Search Root Path**: When configured, this will override the base search root path. (i.e.: kramerica.local's root path is `DC=kramerica,DC=local`, but if you wanted to use only the OU "Users", you would specify `CN=Users,DC=kramerica,DC=local`.  This value will override what is generated from the "Domain Controller Host".
 - **User LDAP Filter**: (default is `(objectCategory=user)`) The filter used to identify an entry as a user.
 - **gMSA LDAP Filter**: (default is `(objectCategory=msDS-GroupManagedServiceAccount)`) The filter used to identify a Group Managed Service account.  This is only used when "Include gMSA" is enabled.
 - **Group LDAP Filter**: (default is `(objectCategory=group)`) The filter used to identify a group.
 - **User name Property Value**: (default is `sAMAccountName`) The LDAP property to identify a user name.
 - **Group Name Property Value**: (default is `name`) The LDAP property to identify a group name.
 - **Display Name Value**: (default is `displayName`) The LDAP property to identify a user's display name.
 - **Email Property Value**:  (default is `mail`) The LDAP property to identify a user's email address.
 - **Group Membership Property Value**: (default is `memberof`) Overrides the property that is retrieved when loading users' groups.  This property will only be used when "No Recursion" or "Recursive Search (LDAP/Non-Active Directory)" is set for the group search type. When the group search type is "Recursive Search (Active Directory Only)" a special Active Directory query is used to find groups.
 
 ### Advanced
- **NETBIOS name mapping**: By default, the global catalog for the domain will be queried to determine any mappings. A list of key/value pairs that map NETBIOS names to domain names may also be specified (one per line); e.g. `KRAMUS=us.kramerica.local`.  If the mapping is not found in this list, it will then query the global catalog to find the mapping
- **Search Group Method**: By default, only users' direct group memberships are considered for permissions, not the groups that their groups belong to; enabling this option will check group memberships recursively, but this may cause reduced performance.  The following values can be selected:
   - **No Recursion**: Users' direct group memberships are queried
   - **Recursive Search (LDAP/Non-Active Directory)**: Will recursively search for users' groups.  This will make multiple LDAP queries and only reccommended to use when not using Active Directory.  This also will throw errors if you have circular group dependencies.
   - **Recursive Search (Active Directory Only)**: Will recursively search users' groups using a special Active Directory query.  This is more efficient than "Recursive Search (LDAP/Non-Active Directory)" because it only has to make one call and handles circular group dependencies, but it will only work with Active Directory.
- **Include gMSA**: When this is checked, Group Managed Service Accounts will be included in the search results by looking for both `user` objects and `msDS-GroupManagedServiceAccount` objects.

## Multiple Domains
Unlike Active Directory / LDAP V3, V4 is connected to a single domain.  To connect to two or more domains, you will need to add multiple user directories.  To add a new user directory, navigate to Adminstration > Manage Security and click the "add new directory / domain" link to the right of User Directories / Domains.

## Multiple Instances (Windows Integrate Authentication)
In some cases your organization might need to have a second instance of ProGet running with different authentication types. For this you will need to add a second website in IIS that points to the same physical path as your original ProGet instance and adjust your authentication settings as needed.

The most typical reason for implementing this would be for companies using Windows Authentication along with Forms authentication at the same time.