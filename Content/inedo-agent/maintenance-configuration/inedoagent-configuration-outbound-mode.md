---
title: "Setting up Outbound Communication"
order: 2
---

As of v49, the Inedo Agent supports an outbound communication mode, so that it can connect to a BuildMaster or an Otter instance rather than only wait for incoming connections. This may be useful for when an agent is hosted on local infrastructure, but BuildMaster or Otter is hosted in a cloud service.

## Configuring Otter

The product (BuildMaster or Otter) must have its Agent Listener enabled; this can be configured on the Admin->Agent Listener Dashboard page. Next, a server must be created in product to refer to the connecting agent. Once created, you will be provided with a secret key used to identify this server.

## Configuring the Inedo Agent

Open the InedoAgent.config file in a text editor, and add a `<Server />` element in the `<Connections>` section:

    <Connections>
      <Server Host="otter.example.com:46336" Key="<secret key from Otter" />
    </Connections>

Then save the file and restart the Inedo Agent service.

## Securing the Connection

When an agent is running in outbound mode, it connects using SSL/TLS to the specified host. The host **must** have a valid trusted certificate.