---
title: Advanced Configuration
sequence: 30
keywords: ldap,active-directory
---

In most installations, the default configuration for the *Active Directory (New)* user directory will *just work*; usually this is the case when the product's server is already joined to the domain associated with the users of the software. There are other cases when a more advanced configuration is required, common examples include:

{.docs}
- Your Inedo product is not installed on the domain, or users are located in a different domain altogether
- NETBIOS mappings cannot be resolved correctly
- Trust relationships with domains that have common/shared user names
- External domain authentication (hosted LDAP)

For advanced configuration, select the *Change User Directory* link on the Administration page, and click *Advanced*. This will display the *Manage User Directories* page containing a list of all user directories configured in the system. Selecting the *Active Directory (New)* directory will open the settings window for that directory. The following options are configurable:

{.docs}
- Name - a simple name to identify the user directory in the UI
- Domain controller host - *(InedoCore v1.0.6+)* the host name or IP address of the domain controller or LDAP server. When specified, LDAP search queries will use this as the root of the search query (e.g. `LDAP://ldap.corp.example.com/`) whereas the default simply uses the root (i.e. `LDAP://`)
- Search mode - *current domain* refers to domain joined to the Inedo product's server
    {.docs}
    - Current domain only - searches only within the domain of the current user credentials in effect for the security context under which the application is running
    - All trusted domains - searches all domains with an Inbound or Bidirectional Trust relationship of the current user credentials in effect for the security context under which the application is running
    - Specific list... – searches an explicit list of newline-separated domains with optional credentials named after a comma. The format for this value is: `domain.local,CredentialName`, where `,CredentialName` is optional to use a different security context than your product is running as. Configuring username/password credentials can be done from the *Manage User Directories* page.
- NETBIOS name mapping – by default, the global catalog for the domain will be queried to determine any mappings. A list of key/value pairs that map NETBIOS names to domain names may also be specified (one per line); e.g. `KRAMUS=us.kramerica.local` and if any value is specified, the automatic query is not performed, so all NETBIOS names must be specified.
- Search recursively - *(InedoCore v1.0.11+)* - by default, only users' direct group memberships are considered for permissions, not the groups that their groups belong to; enabling this option will check group memberships recursively, but this may cause reduced performance
- Include gMSA - *(InedoCore v1.5.0+)* - when this is checked, Group Managed Service Accounts will be included in the search results by looking for both `user` objects and `msDS-GroupManagedServiceAccount` objects

When locating users in the directory, include Group Managed Service Accounts.

## Multiple Instances {#multiple-instances}
In some cases your organization might need to have a second instance of ProGet running with different authentication types. For this you will need to add a second website in IIS that points to the same physical path as your original ProGet instance and adjust your authentication settings as needed. 

The most typical reason for implementing this would be for companies using Windows Authentication along with Forms authentication at the same time. 

## Testing the Configuration {#testing-config}

#### *Test searching*

Enter the first few characters of a username and click *Test*. This will query the directory for any *samaccountname* or *user principal name* that begins with those characters, and write any debug information to the screen.

#### *Test get user*

Enter the user name in any of the formats described above to see whether the user or group is found. If not, that user will not be able to log in or be granted any privileges.
