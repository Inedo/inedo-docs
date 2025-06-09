---
title: "Configuring & Maintaining Inedo Products"
order: 4
---

The [Inedo Hub](/docs/installation/windows/desktophub-overview) allows you to configure certain settings and see the status of your Inedo products. This document explains how to configure and maintain your Inedo product through the Inedo Hub. 

## Service Tab
The Services tab displays the status of all Windows services for each installed product. The Services tab for ProGet is shown below, but it is identical for every Inedo product. 

If everything is in order, then you should see "Running" displayed next to your Widows services.

![Services Tab](/resources/docs/serv-config-services.png)

If something has disrupted the service, then the status will change from "Running" to "Stopped".

![Stopped Service](/resources/docs/serv-config-stoppedservice.png)

To start or stop a service, use the Windows "Services" tool.

## Configuration Tab

The Configuration tab displays entries stored in each product's [configuration file](/docs/installation/configuration-files). 

The configuration file stores a small number of key/value pairs that are unsuitable for storage in the associated database, such as: connection string, integrated web server configuration, encryption key, and web server URLs.

![Configuration Tab](/resources/docs/serv-config-configuration.png)

| Configuration Setting | Description|
| --- | --- |
| Connection String | An expression that contains the parameters required for an Inedo product to connect a server |
| Encryption Key | A randomly generated key tied to your Inedo product instance |
| Integrated Web Server Enabled | A checkable box indicating if an integrated web server is enabled |
| Web Server URLs | The web server URL |

**The values under the Configuration tab can be edited by clicking "Change Settings"**.

## Logs Tab
The Logs tab in the Inedo Hub provides a history of installations done in the Inedo Hub.  

![Inedo Hub Logs](/resources/docs/configuring-logs.png)

These can be useful to send to our support team, or trace when a version of a product was installed.

## Offline Installer
The easiest way to install Inedo products is to use the Inedo Hub, however we do also have instructions on how to [create an offline installer](/docs/installation/windows/desktophub-overview/desktophub-offline) in the case you have no internet access. 
