---
title: "Troubleshooting the Web Server Configuration"
order: 7
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