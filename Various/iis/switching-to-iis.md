---
title: Switching to IIS from Integrated Web Server
subtitle: Hosting Through IIS Instead of the Integrated Web Server
sequence: 200
keywords: installation, iis
---

When you install any of our products using the installer, you're presented with two application hosting options: Integrated Web Server or IIS Web Application.

The question of which to choose comes down to simplicity or robustness, except for load-balanced or High Availability installations of ProGet which require IIS. The Integrated Web Server is easy to install (and obviously doesn't require IIS installed), while an IIS Web Application offers a whole lot more advanced hosting features and configurations (but of course requires IIS).

If you've started with the Integrated Web Server and would like to upgrade your hosting to an IIS Web Application, all it takes is a few easy steps.

## 1. Change Configuration

The service looks in configuration to determine whether the integrated web server should be started.

### BuildMaster
1. Open the [configuration file](/docs/buildmaster/installation/config-files)
2. Change the value of the `Enabled` attribute to `false` on the key `WebServer`
   1.  In BuildMaster versions v6.0.6 and below you will need to change the value of the key named `IntegratedWebServer.Enabled` to `False` 

### Otter
1. Open the [configuration file](/docs/otter/installation/config-files)
2. Change the value of the `Enabled` attribute to `false` on the key `WebServer`
   1.  In Otter versions v2.0.8 and below you will need to change the value of the key named `IntegratedWebServer.Enabled` to `False` 

### ProGet v5.1.0+
1. Open the [configuration file](/docs/proget/installation/config-files)
2. Change the value of the `Enabled` attribute to `false` on the key `WebServer`


### ProGet Prior to v5.1.0

Before ProGet v5.1.0, the integrated web server's configuration was stored in the database.

1. Sign in to ProGet with an account that has administrative privileges
2. Click the *Admin* link (top right corner of the navigation
3. Click on *Advanced Settings* under the System Settings options
4. Change the `UseIntegratedWebServer` key to `False`

## 2. Uninstall the Integrated Web Server (IWS)

You will need to uninstall the Integrated Web Server (IWS) for each product you are looking to migrate to IIS.

### BuildMaster

1. Open a Command Prompt with administrative privileges
2. Navigate to the service directory `C:\Program Files\BuildMaster\Service`
3. Stop the IWS Service by typing `net stop inedobmwebsrv` 
4. Uninstall the IWS Service by typing `bmservice UninstallWebServer`

### Otter

1. Open a Command Prompt with administrative privileges
2. Navigate to the service directory `C:\Program Files\Otter\Service`
3. Stop the IWS Service by typing `net stop inedootterwebsrv`
4. Uninstall the IWS Service by typing `otter.service.exe UninstallWebServer`

### ProGet

1. Open a Command Prompt with administrative privileges
2. Navigate to the service directory `C:\Program Files\ProGet\Service`
3. Stop the IWS Service by typing `net stop inedoprogetwebsvc`
4. Uninstall the IWS Service by typing `proget.service.exe UninstallWebServer`

## 3. Create IIS Web Application {#create-application}

You will need to setup a new Application Pool and WEb Site for each product in IIS.

### Appication Pool

To create a new Applicaiton Pool, you will need to perform the following steps.

1. Open the IIS Manager
2. Create a new Application Pool named `BuildMaster`, `ProGet`, or `Otter` with the following minimum configuration:

{.docs}
 - **.NET CLR Version** - 4.0
 - **Managed Pipeline Mode** - Integrated is recommended, but Classic will also work

Example PowerShell command to create an app pool for ProGet:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" add apppool /name:ProGet /managedRuntimeVersion:v4.0 /managedPipelineMode:Integrated /processModel.identityType:SpecificUser /processModel.userName:ProGetServiceUser@domain /processModel.password:<account-password>
```

Additional instructions for creating application pools can be found here: [Create an Application Pool in IIS 7.x](http://technet.microsoft.com/en-us/library/cc731784(v=WS.10).aspx)

### Web Site

To create a new Applicaiton Pool, you will need to create a new Website (virtual directories are not supported) named BuildMaster, ProGet, or Otter with the following minimum configuration:

{.docs}
 - **Physical Path** - the path on disk where the web application files are located (by default `C:\Program Files\[ProductName]\Web`)
 - **Application Pool** - the application pool to use (by default `[ProductName]`)
 - **Binding** - the combination of IP address(es) and port(s) used to access the product over the network

_Note that any of these values can be edited once they are created, for example, to add SSL certificates, update the port that ProGet is listening on, etc._

Example PowerShell command to create a ProGet site:

```
&"C:\Windows\System32\inetsrv\appcmd.exe" add site /name:ProGet /physicalPath:"C:\Program Files\ProGet\Web" /bindings:"http/*:80:" /applicationDefaults.applicationPool:ProGet
``` 

You may adjust other IIS site settings as desired.

Your product will now be hosted through IIS. The products aren't very particular about how they are hosted as long as the [prerequisite roles & features are installed](roles-and-features), so virtually any of the settings within the IIS Application or Application Pool can be changed to suit your needs.