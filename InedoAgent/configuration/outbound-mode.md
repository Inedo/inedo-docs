---
title: Running in Outbound Mode
subtitle: Setting up Outbound Communication
sequence: 30
keywords: inedo, inedo agent

---

As of v49, the Inedo Agent supports an outbound communication mode, so that it can connect to an Otter instance instead of only wait for incoming connections. This may be useful for when an agent is hosted on local infrastructure, but Otter is hosted in a cloud service.

## Configuring Otter

Otter must have its Agent Listener enabled; this can be configured on the Admin->Agent Listener Dashboard page. Next, a server must be created in Otter to refer to the connecting agent. Once created, Otter will provide a secret key used to identify this server.

## Configuring the Inedo Agent

Open the InedoAgent.config file in a text editor, and add a `<Server />` element in the `<Connections>` section:

    <Connections>
      <Server Host="otter.example.com:46336" Key="<secret key from Otter" />
    </Connections>

Then save the file and restart the Inedo Agent service.

## Securing the Connection

When an agent is running in outbound mode, it connects using SSL/TLS to the specified host. The host **must** have a valid trusted certificate.