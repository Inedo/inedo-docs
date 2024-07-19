---
title: "Hosting Through IIS Instead of the Integrated Web Server"
order: 4
---

When you install any of our products using the installer, you're presented with two application hosting options: Integrated Web Server or IIS Web Application.

:::(Error) (💀 IIS is Not Recommended 💀)
Following Microsoft's guidance to [no longer use IIS for modern .NET applications](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel), we no longer recommend using IIS to host our products.  

Instead, you should use the Integrated Web Server, which is ours (and Microsoft's) new recommended web server. Although IIS is still supported, there are no benefits aside from a more familiar user interface. 
:::

If you've started with the Integrated Web Server and would like to upgrade your hosting to an IIS Web Application, all it takes is a few easy steps.

## 1. Change Configuration

The service looks in configuration to determine whether the integrated web server should be started.

The easiest way to change this is to navigate to the [Configuration Tab of the Inedo Hub](/docs/installation/windows/inedohub-configure-products#configuration-tab), click "Change Settings..." and then check the "Integrated web server enabled" checkbox.

![Configuration tab](/resources/docs/serv-config-configuration.png)

Alternatively, you can also edit the [configuration file](/docs/installation/configuration-files) and change the value of the `Enabled` attribute to `false` on the key `WebServer`.

### Legacy Versions (2017 and Earlier)

In older versions of Inedo Products, the integrated web server's configuration was stored in the database. These had to be changed as follows:

1. Sign in to the product with an account that has administrative privileges
2. Click the *Admin* link (top right corner of the navigation
3. Click on *Advanced Settings* under the System Settings options
4. Change the `UseIntegratedWebServer` key to `False`


## 2. Uninstall the Integrated Web Server (IWS)

You will need to stop and uninstall the Integrated Web Server (IWS) for the product you are looking to migrate to IIS.

1. Identify the name of the web service name, which is typically:
   * BuildMaster: ` inedobmwebsrv` 
   * Otter: `inedootterwebsrv` 
   * ProGet `inedoprogetwebsvc` 
2. Open a Command Prompt with administrative privileges
3. Stop the IWS Service by typing `sc stop «service-name»`
6. Uninstall the IWS Service by typing `sc delete «service-name»`

## 3. Create IIS Web Application

You will need to setup a new Application Pool and Web Site for each product in IIS.

### Addtional v2022 Requirements

Starting in ProGet v2022, Otter v2022, and BuildMaster v2022, the [ASP.NET 6 Core Runtime Hosting Bundle](https://dotnet.microsoft.com/en-us/download/dotnet/6.0) is required to host the web application in IIS.  If you have not previously installed this, you will need to download the latest .NET 6.0 hosting bundle and install it prior to configuring your application in IIS.  

:::(info) (Note:)
Inedo Hub automatically installs the hosting bundle when selecting IIS hosting during a new install.
:::

::: (Warning) (Important!)
If the .NET 6 SDK or Hosting Bundle was installed prior to installing IIS, you will need to repair the SDK or Hosting Bundle after you install IIS.
:::

### Application Pool

To create a new Application Pool, you will need to perform the following steps.

1. Open the IIS Manager
2. Create a new Application Pool named `BuildMaster`, `ProGet`, or `Otter` with the following minimum configuration:

 - **.NET CLR Version** - 4.0
 - **Managed Pipeline Mode** - Integrated is recommended, but Classic will also work

Example PowerShell command to create an app pool for ProGet:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" add apppool /name:ProGet /managedRuntimeVersion:v4.0 /managedPipelineMode:Integrated /processModel.identityType:SpecificUser /processModel.userName:ProGetServiceUser@domain /processModel.password:<account-password>
```

Additional instructions for creating application pools can be found here: [Create an Application Pool in IIS 7.x](http://technet.microsoft.com/en-us/library/cc731784(v=WS.10).aspx)

:::(info) (Note:)
If the Inedo product's SQL Server connection string is configured to use integrated security, the application pool's process model identity requires proper access to your SQL Server.
:::

### Web Site

To create a new Application Pool, you will need to create a new Website (virtual directories are not supported) named BuildMaster, ProGet, or Otter with the following minimum configuration:


 - **Physical Path** - the path on disk where the web application files are located
     - v2022 and Later: `C:\Program Files\«product-name»\Service`
     - Earlier versions: `C:\Program Files\«product-name»\Web`
 - **Application Pool** - the application pool to use (by default `«product-name»`)
 - **Binding** - the combination of IP address(es) and port(s) used to access the product over the network

_Note that any of these values can be edited once they are created, for example, to add SSL certificates, update the port that ProGet is listening on, etc._

Example PowerShell command to create a ProGet 2022 site:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" add site /name:ProGet /physicalPath:"C:\Program Files\ProGet\Service" /bindings:"http/*:80:" /applicationDefaults.applicationPool:ProGet
``` 

You may adjust other IIS site settings as desired.

Your product will now be hosted through IIS. The products aren't very particular about how they are hosted as long as the [prerequisite roles & features are installed](/docs/installation/installing-on-iis/various-iis-configuring-iis-roles-and-features){target="_blank"}, so virtually any of the settings within the IIS Application or Application Pool can be changed to suit your needs.
