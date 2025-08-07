---
title: "Active Directory (v5)"
order: 2
---


To mitigate the risk of authentication errors after we make major changes to our Active Directory / LDAP integration code, we create a new "user directory" type that can be configured side-by-side with the existing types. This article documents the advanced configuration of the **Active Directory (v5)** user directory, which is available in 2024 and later versions.

In ProGet 2024, BuildMaster 2024, and Otter 2024, we have added a new Active Directory User Directory *Active Directory (v5)* to improve performance in connection to Microsoft Active Directory and other LDAP providers that implement Microsoft's Active Directory conventions.  This improves upon "Active Directory v4 (New)" by removing generic LDAP support and focusing on optimizing connections to Microsoft Active Directory. For OpenLDAP and generic LDAP support, see [OpenLDAP/Generic LDAP](/docs/installation/security-ldap-active-directory/various-ldap-openldap).

In most installations, the default configuration for the *Active Directory (v5)* user directory will *just work*; usually this is the case when the product's server is already joined to the domain associated with the users of the software. There are other cases when a more advanced configuration is required, common examples include:

- Your Inedo product is not installed on the domain, or users are located in a different domain altogether
- NETBIOS mappings cannot be resolved correctly
- Trust relationships with domains that have common/shared user names
- External domain authentication (hosted LDAP)
- You would like to enable LDAPS support


## Configuring your User Directory
For advanced configuration, navigate to Administration > Manage Security and click "edit" to the right of "Active Directory v5 (New)".  If you do not see this user directory, you will need to add a new "V5: Active Directory" directory.

::: (Info) (Note:)
Users and groups configured in tasks and permissions are compatible with v3, v4, and v5, allowing easy switch-over between user directories.  If you are using an LDAP server that does not follow Active Directory conventions, you should use the [OpenLDAP/Generic LDAP](/docs/installation/security-ldap-active-directory/various-ldap-openldap) user directory instead.
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
 
 ### Advanced
- **NETBIOS name mapping**: By default, the global catalog for the domain will be queried to determine any mappings. A list of key/value pairs that map NETBIOS names to domain names may also be specified (one per line); e.g. `KRAMUS=us.kramerica.local`.  If the mapping is not found in this list, it will then query the global catalog to find the mapping
- **Include gMSA**: When this is checked, Group Managed Service Accounts will be included in the search results by looking for both `user` objects and `msDS-GroupManagedServiceAccount` objects.

## Multiple Domains
Active Directory V5 is connected to a single domain.  To connect to two or more domains, you will need to add multiple user directories.  To add a new user directory, navigate to Administration > Manage Security and click the "add new directory / domain" link to the right of User Directories / Domains.

## Multiple Instances (Windows Integrated Authentication)
In some cases your organization might need to have a second instance of ProGet running with different authentication types. For this you will need to add a second website in IIS that points to the same physical path as your original ProGet instance and adjust your authentication settings as needed.

The most typical reason for implementing this would be for companies using Windows Authentication along with Forms authentication at the same time.