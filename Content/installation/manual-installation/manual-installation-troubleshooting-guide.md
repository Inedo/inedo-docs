---
title: "Manual Installation Troubleshooting Guide"
order: 4
---

There are a lot of moving pieces involved in a manual installation (particularly ones that the installer would have handled for you). Here are the most common manual installation errors as we have noted from our various support channels.

### Cannot connect to the database
Perhaps the most common problem is that the SQL Server database is inaccessible. Check the following:

 - if the connection string uses integrated authentication, the user account connecting (i.e. the application pool identity or Windows Service logon user) have a database login, an associated database user, and that user is granted the `«Inedo-product»User_Role`
 - the user account is a domain account or SQL account when accessing a database over the network (i.e. don't use `NETWORK SERVICE` and connect to a remote database)
 - the connection string exists in the Inedo Product configuration file and is valid (i.e. has the correct server name, database name, and includes the instance name e.g. `SQLEXPRESS`)
 - the user specified in the connection string has a database login, an associated database user, and that user is granted the `«Inedo-product»User_Role`

### Windows Service will not start
Typically when the Inedo product Windows Service does not start, it is database connection-related. Any errors logged by the Inedo product Service can be found in the Windows Event Log under *Windows Logs* > *Application*.

Alternatively, you can run the service interactively (i.e. in a command window) to view live log output. Simply ensure the `INEDO«INEDO-PRODUCT»SVC` service is stopped, then run the following command as an administrator:

**BuildMaster:**
```
PS C:\Program Files\BuildMaster\Service> .\BuildMaster.Service.exe run --mode=serviceonly
```

**ProGet:**
```
PS C:\Program Files\ProGet\Service> .\ProGet.Service.exe run --mode=serviceonly
```

**Otter:**
```
PS C:\Program Files\Otter\Service> .\Otter.Service.exe run --mode=serviceonly
```

If there is a difference in behavior when run interactively vs. running the service, there may be a problem with the user account that the Inedo product service is running as.

### Can't control the service from within your Inedo product

Visit the [Service Messenger](/docs/installation/high-availability-load-balancing/installation-cluster-management) documentation for enabling inter-process communication between the Inedo product Website and Inedo product Service.

### Extensions failing to load
Extensions failing to load is commonly caused by:
 - missing/invalid extensions path setting (check *Administration* > *Advanced Settings*)
 - the service not being restarted after the extension path is changed
 - the user account the application pool identity and Windows Service logon user has read/write/modify access to the extensions folder (`Extensions.ExtensionsPath`) and the common cache path (`Extensions.CommonCachePath`)

Load errors for extensions are logged in the Diagnostic Center (check *Administration*).

### Additional help & support
Inedo offers several support mechanisms if you have any other questions regarding a manual installation:

 - Support Home - [https://inedo.com/support](https://inedo.com/support){target="_blank"}
 - Community Forums - [https://forums.inedo.com](https://forums.inedo.com){target="_blank"}
 - Support Tickets (*paid users only*) - [https://my.inedo.com/tickets/new](https://my.inedo.com/tickets/new){target="_blank"}