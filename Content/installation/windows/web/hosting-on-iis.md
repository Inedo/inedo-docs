---
title: "Hosting on IIS"
order: 6
---

:::(Error) (💀 IIS is Not Recommended 💀)
Following Microsoft's guidance to [no longer use IIS for modern .NET applications](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/servers/kestrel), we no longer recommend using IIS to host modern versions (2022+) of our products.  

Instead, you should use the Integrated Web Server, which is our (and Microsoft's) recommended web server.
:::

Beginning in version 2025.0+ of our products, IIS is no longer a supported installation option.  IIS may still be used as a reverse-proxy for the Integrated Web Server, but you can no longer select that as an installation option.  To use IIS as a reverse-proxy, please see our [HOWTO: Use IIS a Reverse-Proxy](/docs/installation/windows/web/howto-using-iis-as-proxy).

If you are still using a product version of 2024 or lower and are still hosting in IIS, please refer to our archived documentation:
- [Hosting Through IIS Instead of the Integrated Web Server (archive.org)](https://web.archive.org/web/20231209233952/https://docs.inedo.com/docs/various-iis-switching-to-iis).
- [Configuring IIS Roles & Features for Inedo Products](https://github.com/Inedo/inedo-docs/blob/44a99e458fe3ac2972ffa7e74b48de8fd342ed18/Content/installation/installing-on-iis/various-iis-configuring-iis-roles-and-features.md)
- [Disabling WebDAV in IIS](https://github.com/Inedo/inedo-docs/blob/44a99e458fe3ac2972ffa7e74b48de8fd342ed18/Content/installation/installing-on-iis/various-iis-disabling-webdav.md)
