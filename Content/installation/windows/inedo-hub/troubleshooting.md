---
title: "Troubleshooting"
order: 5
---

When an Inedo product installation fails, Inedo Hub will usually show the product with the failed installation as uninstalled. Note that after an upgrade, **no data is deleted** from your original instance, so you may safely reinstall the product by specifying the same database connection information.

However, if a failed install/upgrade causes the software to appear already installed, or even causes Inedo Hub to fail to load, perform the following steps:

1. Ensure your database/files are backed up, just to be safe
2. Try to uninstall the product (this will **not** delete any of your data)
3. Uninstall Inedo Hub
4. Stop all services related to the installed product (these will all start with INEDO in the service manager)
5. Delete all data from `C:\ProgramData\upack`
6. Delete the product installation from `C:\Program Files\<ProductName>` if necessary
7. Download Inedo Hub and install
8. Reinstall the product using your original database


## Troubleshooting Inedo Hub Installation
If Inedo Hub itself fails to install, it most likely is not able to download the installation package. The Inedo Hub installer attempts to download the latest Inedo Hub from our servers on startup, so if there are connectivity or proxy issues, it will fail.
::: (INFO) 
If you need to install an Inedo product in an offline environment with no Internet connectivity, see the [offline installation guide](/docs/installation/windows/inedo-hub/offline).
:::
To troubleshoot Inedo Hub installation, first verify basic Internet connectivity on your server. If you can connect to [proget.inedo.com](https://proget.inedo.com){target="_blank"} using your browser and you are using a proxy server, you may need to manually specify proxy settings.

Inedo Hub attempts to use the system-configured proxy settings in Windows, but this is a per-user setting historically associated with Internet Explorer, and may not be reliably detected in all environments. If you are using a proxy and can't connect, try specifying it manually by editing or creating the `C:\ProgramData\Inedo\SharedConfig\DesktopHub.config` file in a text editor and adding the `Proxy` element to the root `DesktopHub` element:
```
<DesktopHub>
    <Proxy Enabled="true" ProxyAddress="<proxy server>" />
</DesktopHub>
```

`<proxy server>` specifies the DNS name or IP address of your proxy server and a port number. For example: `my.proxy.example.com:8080`

Save the file, then try installing Inedo Hub again.