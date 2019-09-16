---
title: Visual Studio Extension (.vsix)
sequence: 150
keywords: proget,feeds,vsix
---
A VSIX feed in ProGet stores Visual Studio extensions, which can then be installed directly from Visual Studio.

Visual Studio does not allow disabling or removing the standard extension gallery, and this gallery actually uses an internal API that is different from the documented Atom VSIX feed ProGet exposes, so currently a connector to the Visual Studio Gallery cannot be created in ProGet. However, connectors to other Atom VSIX feeds are supported.

There is no official API defined by VSIX for adding packages. If you would like to upload a .vsix extension from a tool or script, you can simply PUT or POST the extension to the feed API endpoint URL.

```
 # PowerShell example
Invoke-RestMethod https://proget.example.com/vsix/FeedName `
-InFile .\MyExtension.vsix `
-Headers @{"Authorization" = "Basic " + [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes("api:xxxxxxxxxxxxxx"))}
```

Note that PowerShell does not support TLS 1.2 (defined by [RFC 5246](https://tools.ietf.org/html/rfc5246), August 2008) by default. If your server does not allow TLS 1.0 connections (required for PCI compliance since June 2018), you will need to enable TLS 1.2 before running the above command.

See [KB#1161](https://inedo.com/support/kb/1161/tls-v12-configuration-and-connection-errors) for instructions on how to enable TLS 1.2.


#### VSIX Limitations
##### Windows Integrated Authentication
Visual Studio requires "Anonymous" access to a ProGet instance. This is not possible if your instance of ProGet has Integrated Authentication enabled. To workaround this, you can set-up a second site in IIS without Windows Integrated Authentication enabled that points to the same path on disk.
