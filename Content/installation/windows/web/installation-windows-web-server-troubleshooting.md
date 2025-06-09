---
title: "Troubleshooting the Web Server Configuration"
order: 8
---

::: (Internal) (TODO)
Add troubleshooting section to HTTPS and IWS docs.  Should include:
- Commands to see current bindings
- https binding is binding as HTTP instead
   - thinks to check, like private keys, etc...
   - if hostname, certificate bound
- Can't connect, but no errors in the windows event log
:::

## The integrated web server service is running, but I cannot connect
This typically happens when there is a problem with the HTTP/HTTPS binding.  Any errors that occur in the binding will be displayed in the Windows Event Log under Windows Logs > Application.  The most common error is:

```
Failed to run Integrated Web Server.
System.Net.Sockets.SocketException (10013): An attempt was made to access a socket in a way forbidden by its access permissions.
   at System.Net.Sockets.Socket.UpdateStatusAfterSocketErrorAndThrowException(SocketError error, String callerName)
   at System.Net.Sockets.Socket.DoBind(EndPoint endPointSnapshot, SocketAddress socketAddress)
   at System.Net.Sockets.Socket.Bind(EndPoint localEP)
   ...
```

This is commonly caused by:
- The integrated web server is configured to use a port that is already bound
- The integrated web server is configured to use a port below 1024, but the windows service is not running as a local administrator
- The integrated web server is configured to use a hostname, but is not bound in Windows

The most common way to fix this is to run a `netsh add urlacl` to allow the Windows service account to bind to that host name and/or port.  For example: 
- To bind to a host name: `netsh http add urlacl url=https://proget.myserver.com:443/ user="Network Service"` 
- To bind to a port below 1024: `netsh http add urlacl url=https://+:443/ user="Network Service"` 

## Cannot configure SSL in ProGet due to a permissions issue

This typically happens when you are using a non-Administrator level service account (on Windows) to run the service with the Integrated Web Server.  The service account needs Modify rights to the following folders on `C:\ProgramData\ProGet` and `C:\ProgramData\Indeo`.  This account also needs Read access to the certificate within the certificate store.  To configure read permissions:
1. Open the Certificate Manager using Window's MMC
2. Navigate to Certificates (Local Computer) -> YOUR_STORE (ex: Personal) -> Certificates
3. Right click on the certificate and select All Tasks -> Manage Private Keys
4. And give read access to your service account

If you are still having issues, ensure that your service account has the "Log on as a service" privilege.

## Extended Key Usage does not include Server Authentication (OID 1.3.6.1.5.5.7.3.1)
If hosting fails to start with the error `Certificate ... cannot be used as an SSL server certificate. It has an Extended Key Usage extension but the usages do not include Server Authentication (OID 1.3.6.1.5.5.7.3.1).`, then that means that the certificate the was loaded by the Integrated Web Server is missing the Server Authentication (OID 1.3.6.1.5.5.7.3.1) usage.  You will need to regenerate the certificate to include that OID.  

If your certificate has that usage, then you most likely have multiple certificates with the same subject name and your Inedo product is selecting the wrong certificate. See [I have two certificates with the same name and the wrong certificate is selected](#i-have-two-certificates-with-the-same-name-and-the-wrong-certificate-is-selected) for how to handle this.

## I have two certificates with the same name and the wrong certificate is selected
If you have two certificates with the same name, the workaround for this is to edit the [installation configuration file](/docs/installation/configuration-files) and specify the certificate's thumbprint instead of the subject.  This will force the integrated web server to use a  specific certificate.  

#### Example WebServer Element
```
<WebServer Enabled="true" Urls="http://*:2365;https://*:2364"  Thumbprint="0fa73d6fe6bffd12594d617a595d46d0b738903a" Store="My" Location="CurrentUser" />
```

:::(Warn)
When using a certificate's thumbprint, you will need to modify the [installation configuration file](/docs/installation/configuration-files) anytime the certificate is renewed.
:::