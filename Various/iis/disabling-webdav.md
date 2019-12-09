---
title: Disabling WebDAV in IIS
sequence: 300
keywords: installation, iis
---

Some ProGet feed endpoints or other product API endpoints use `PUT` or `DELETE` requests. If the WebDAV module is enabled in IIS, these requests can result in a `405 Method Not Allowed` error. This article details several methods of disabling the WebDAV module.

### 1. Disable the WebDAV Publishing feature in Windows

This is the easiest solution if WebDAV is not desired for any website running on IIS on the server.

#### Windows Server Editions

Open the Server Manager and select "Manage", "Remove Roles and Features", jump to the "Server Roles" section and uncheck the following option: 

Web Server (IIS) > Web Server > Common HTTP Features > WebDAV Publishing

Select "Next" until you can select "Remove" on the Confirmation section. You may need to restart the server for the change to take effect.

#### Windows Desktop Editions

Open a Windows Explorer window and navigate to `Control Panel\All Control Panel Items\Programs and Features`. Select the `Turn Windows features on or off` option in the menu on the left. Uncheck the following option: *Internet Information Services > World Wide Web Services > Common HTTP Features > WebDAV Publishing*. Click OK. You may need to restart the server for the change to take effect.

### 2. Disable WebDAVModule in the Internet Information Services Manager

This solution requires the IIS Manager program to be installed. It does not affect other applications running on IIS.

Open the IIS Manager and navigate to the ProGet site. Double-click the *Handler Mappings* item. Right click `WebDAV` and select *Remove*. Navigate back to the product site. Double-click the *Modules* item. Right click `WebDAVModule` and select *Remove*.

### 3. Manually edit Web.config to disable WebDAV

This solution requires editing an XML configuration file directly, but does not require any extra software.

Edit `Web.config`. By default, it is located in `C:\Program Files\ProGet\Web`. Find the `<modules>` section in the `<system.webServer>` section. Add `<remove name="WebDAVModule" />` before `</modules>`. Find the `<handlers>` section in the `<system.webServer>` section. Add `<remove name="WebDAV" />` before `</handlers>`.

You may need to recycle the app pool or restart IIS for this change to take effect.