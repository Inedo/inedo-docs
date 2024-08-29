---
title: "Visual Studio Extension (.vsix)"
order: 7
---

A VSIX feed in ProGet stores Visual Studio extensions, which can then be installed directly from Visual Studio.

Visual Studio does not allow the default extension gallery to be disabled or removed, and this gallery uses an internal API that is different from the documented Atom VSIX feed that ProGet provides, so no connector to the Visual Studio Gallery can currently be created in ProGet. However, connectors to other Atom VSIX feeds are supported.

There is no official VSIX-defined API for adding packages. If you want to upload a .vsix extension from a tool or script, you can simply pass the extension to the feed API endpoint URL at PUT or POST.

```powershell
# PowerShell example
Invoke-RestMethod https://proget.example.com/vsix/FeedName `
-InFile .\MyExtension.vsix `
-Headers @{"Authorization" = "Basic " + [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes("api:xxxxxxxxxxxxxx"))}
```

Note that PowerShell does not support TLS 1.2 (defined by [RFC 5246](https://tools.ietf.org/html/rfc5246), August 2008) by default. If your server does not allow TLS 1.0 connections (required for PCI compliance since June 2018), you will need to enable TLS 1.2 before running the above command.

See [KB#1161](https://inedo.com/support/kb/1161/tls-v12-configuration-and-connection-errors) for instructions on how to enable TLS 1.2.

## Adding the Feed to Visual Studio

To add a VSIX feed to Visual Studio, an additional extension gallery must be added. To do this, navigate to _Tools > Options > Environment > Extensions_ and click the _Add_ button under _Additional Extension Galleries_. Fill in the name and set the URL to the API endpoint URL of the VSIX feed.

This allows you to install the extensions of your VSIX feed in addition to the extensions in the built-in galleries using the _Manage Extensions_ window.

## VSIX Limitations

### Windows Integrated Authentication

Visual Studio requires "Anonymous" access to a ProGet instance. This is not possible if your instance of ProGet has built-in authentication enabled. To work around this problem, you can set up a second site in IIS without Windows Integrated Authentication enabled, pointing to the same path on disk.