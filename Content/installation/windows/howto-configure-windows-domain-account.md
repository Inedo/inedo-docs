---
title: "HOWTO: Configure Your Inedo Product to Run As a Windows Domain Account"
order: 7
---

Inedo's products can be configured to run as a Windows Domain Account to ensure secure management of your infrastructure and applications. 

This article will walk you through changing the user account for these products, ensuring proper permissions and security settings are applied. Since Inedo products are made up of a web application (whether it’s hosted through IIS or the Integrated Web Server) and a service that orchestrates builds and deployments, the user account must be changed in 2 places. 

The example screenshots are from BuildMaster, but the process will be exactly the same no matter which one of our products you’re using. 

Depending on which product you are using, enter the coinciding product name or abbreviation into each space:

|  Inedo Product Name «NAME» | Abbreviation  «XX» |
|---|---| 
| BuildMaster | BM |
| ProGet | PROGET |
| Otter | OTTER |

 During installation, you can select the user account that your product will run as:
 * Local System
 * Network Service
 * Other

For evaluation purposes it’s generally easiest to select “Local System” as that prevents most permissions issues, but once you’ve determined the exact use case for your installation, you can lock down permissions granularly by changing the user account to a domain user following the least-privilege principle. 

## Step 1: Change the User Account for the Inedo Product's Service:

1. Open the Windows Services Management Console (Start > Run > `services.msc`).

2.  Right-click on the "«NAME» Service (INEDO«XX»SVC)" and select "Properties".

3.  Go to the "Log On" tab and enter the new user account credentials.

![](/resources/docs/inedo-credentials-configure.png){height="" width="50%"}

## Step 2: Change the User Account for IWS or IIS
This step will depend on if you are using Integrated Web Server or IIS

### Step 2a: Integrated Web Server
1. Follow the same steps as for the Windows Service, but locate the "«NAME» Web Server" service (INEDO«XX»WEBSRV).
2. If you are changing to an account that is *not* a machine administrator, add a URL reservation for the user account/for this user account to be able to host a web site on the integrated web server, [following these instructions](/docs/installation/windows/desktophub-overview#product-configuration).

**Note**: Explicit URL reservations are only required when using a non-administrator account and hosting with the integrated web server. 

### Step 2b: IIS
1. Open IIS Manager
2. Select "Application Pools".
3. Right-click on the "«NAME» AppPool" and choose “Advanced Settings…”.
4. Under Process Model, change the identity to the new user account.

![](/resources/docs/inedo-iis-configure.png){height="" width="50%"}

 ## Step 3 (Optional): SQL Server
If using integrated SQL authentication, ensure the new user account has permission to access SQL Server and is assigned the "«NAME» User_Role" role.

## Managed Service Accounts

Inedo products support being run as a [Managed Service Accounts](https://learn.microsoft.com/en-us/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting). However, this requires that your database and application pool also have the ability to run as and accept logins from these accounts. 
