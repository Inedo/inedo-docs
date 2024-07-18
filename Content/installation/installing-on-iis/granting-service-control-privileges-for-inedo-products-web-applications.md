---
title: "Granting Service Control Privileges for Inedo Product’s Web Applications"
order: 6
hidden: true
---

You may find that the Service Status page (`Administration > Manage Service`) in your Inedo product isn't displaying the `stop` and `start` buttons that would otherwise control the Windows service. In older versions, you may also see a message like "The ProGet web application does not have sufficient privileges to query the service status."

The reason for this is that, typically, you need to be need to be running as a machine administrator in order to stop or start a Windows service. However, it's rarely desirable or even permitted to run web applications as a system administrator, which means that the web application typically won't be able to  able to control the service using the web interface.