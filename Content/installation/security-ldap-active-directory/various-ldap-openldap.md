---
title: "OpenLDAP/Generic LDAP"
order: 3
---

In ProGet 2024, BuildMaster 2024, and Otter 2024, we have added a new User Directory *OpenLDAP/Generic LDAP* to better handle connections to OpenLDAP and other LDAP providers that are not Microsoft's Active Directory.  This differs from "Active Directory v4 (New)" because it does not do any domain network discovery, it requires the *distinguished name* for an LDAP User and its password, and it uses the specified LDAP queries instead of an Active Directory first approach.

In most installations of OpenLDAP, the default configuration for the *OpenLDAP/Generic LDAP* user directory will *just work* with only needing to supply the host address and the distinguished name for an LDAP user and its password.  For other LDAP based directories, you may need to modify the attributes and LDAP queries, specifically the object class.

## Configuring your User Directory
To add a new *OpenLDAP/Generic LDAP* user directory, navigate to Administration > Manage Security, click "add new directory / domain", click "Other Types", and Select "OpenLDAP/Generic LDAP".

### General
- **Name**: A simple name to identify the user directory in the UI
- **Domain**: The domain suffix to use for LDAP queries (ex: `kramerica.local`).
- **LDAP Connection**: The connection protocol to connect to the LDAP server with.  The following values can be selected:
   - **Use LDAP** (default): Connects via the standard unencrypted LDAP protocol
   - **Use LDAPS**: Connects via LDAPS (LDAP over TLS/SSL) protocol
   - **Use LDAPS and bypass certificate errors**: Connects via LDAPS (LDAP over TLS/SSL) protocol but ignores any certificate warnings.  LDAP servers typically use domain signed certificates that not all servers, like our Docker images, trust by default.  This allows you to still connect via LDAPS and ignore the certificate error.
- **Bind DN**: The distinguished name for the LDAP user to use when connecting to your LDAP server.  (ex: `CN=ProGetAdmin,CN=Users,DC=kramerica,DC=local`)
- **Password**: The password to use when connecting to your LDAP server.

### LDAP User Filters
- **User Search Base**: (default is the root of your directory based on the Domain) The base DN to use when searching for users.
- **Users**: (default is `(&(objectClass=inetOrgPerson)(uid=%s))`) The LDAP query to use to find and search for users.  A `%s` is required and will be replaced with the search string or username for a user.
- **List User's Groups**: (default is `(|(&(objectClass=groupOfNames)(member=%s))(&(objectClass=groupOfUniqueNames)(uniqueMember=%s)))`) The LDAP query to use to find a user's group.  A `%s` is required and will be replaced with the distinguished name for a user.

### LDAP Group Filters
- **Group Search Base**: (default is the root of your directory based on the Domain) The base DN to use when searching for groups.
- **Groups**: (default is `(&(objectClass=groupOfNames)(cn=%s))`) The LDAP query to use to find and search for groups.  A `%s` is required and will be replaced with the search string or name for a group.
- **List Group's Members**: (default is `(&(objectClass=inetOrgPerson)(memberof=%s))`) The LDAP query to use to find a members of a group.  A `%s` is required and will be replaced with the distinguished name for a group.

### LDAP Object
- **User name Property Value**: (default is `uid`) The LDAP property to identify a user name.
- **Display Name Value**: (default is `displayName`) The LDAP property to identify a user's display name.
- **Email Property Value**:  (default is `mail`) The LDAP property to identify a user's email address.
- **Group Name Property Value**: (default is `cn`) The LDAP property to identify a group name.

### Advanced
- **NETBIOS name mapping**: By default, NETBIOS formats of `domain\user` is not supported.  Creating a mapping in this field, enables this format. A list of key/value pairs that map NETBIOS names to domain names may be specified (one per line); e.g. `KRAMUS=us.kramerica.local`.
- **LDAP Port**: This overrides the port used to connect to the LDAP server with.  By default LDAP use 389 and LDAPS uses 636.
 
## Multiple Domains
To connect to two or more domains, you will need to add multiple user directories.  To add a new user directory, navigate to Adminstration > Manage Security and click the "add new directory / domain" link to the right of User Directories / Domains.  Then click "Other Types", and Select "OpenLDAP/Generic LDAP"

