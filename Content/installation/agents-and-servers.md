---
title: "Managing Agents and Servers"
order: 10
---

BuildMaster and Otter provide several options for connecting to your servers. This gives you a lot of flexibility for configuring it to work with the existing servers and network configuration.

No matter which communication protocols you chose for which server, the servers will look the same inside of BuildMaster or Otter, and users won't ever need to worry about remembering credentials or IP Addresses. 

This article will discuss the various options for connecting to your servers, and how to decide which options in which case.

## The Inedo Agent vs "Agentless" Communication
The first decision to make is whether to use the [Inedo Agent](/docs/inedo-agent/inedoagent-overview) or a general-purpose communication channel like WMI (Windows) or SSH (Linux). This general-purpose channel is often called "agentless" communication.

### Reasons to choose the Inedo Agent
Unlike general-purpose channels, the Inedo Agent was designed for the sole purpose of communicating with Inedo products. This offers several benefits, including:
* **Flexibility**; both Inbound- and Outbound communication is supported, which can greatly simplify router and firewall configuration across networks
* **Easier to configure**; just install and use without needing to configuring domain credentials or login certificates
* **Performance**; the communication protocol is highly-optimized, as it's only designed to work with our products
* **Security**; while all channels are encrypted, the Inedo Agent has a significantly lower attack surface than WMI or SSH, and can be configured with lower permissions

### Reasons to choose "Agentless" Configuration
The main reason not to use the Inedo Agent is convenience. You may already have WMI or SSH enabled, and don't want to install an additional agent.

## Inbound vs Outbound Communication
When two computers need communicate with each other, one of the computers needs to *initiate* communication, and the other needs to *listen* for communication. It's not unlike two people communicating directly, using a phone: someone had to start (*initiate*) the call, and the other had to wait (*listen*) for the call to come in.

BuildMaster and Otter support both modes of communication: it can *initiate* communication with your servers (this is called *inbound* communication from your servers' perspective), or it can *listen* for communication from your servers (which is called *outbound*). 

:::(Warning)
The words "inbound" and "outbound" terminology can be a little confusing when discussing communication, because it depends on which side you're looking from. When we use the words "inbound" and "outbound", we always look at it from the server's perspective (not BuildMaster's or Otter's perspective).
:::

### Reasons to Initiate Communication
This is the default option, and it's generally the easiest to configure. BuildMaster or Otter will initiate communication with your servers on-demand, and the servers will simply wait for communication from BuildMaster/Otter.

:::(Info)
Note that this is the only option if you're using a general-purpose communication channel like WMI (Windows) or SSH (Linux). This general-purpose channel is often called "agentless" communication.
:::

You simply need to make sure that BuildMaster or Otter can make the connection to your server, which typically involves opening a firewall port on that server and making sure you have the right credentials configured.

### Reasons to Listen for Outbound Communication
It's sometimes not feasible to have BuildMaster or Otter initiate communication with your server. For example, if you have a lot of servers behind a single router with a single IP address, then you may not be able to directly contact one of those servers without configuring port-mapping or other work-arounds on the router.

This is where listening for outbound communication comes in. This is a bit trickier to configure, as you'll need to install an SSL certificate on the BuildMaster/Otter server, and make sure that it is able to listen for communication on a specific port.

## The LOCALHOST Server
If you're using BuildMaster or Otter to interact with the server it's installed on, you can just set it up using a local agent. This uses the same process/identity that the [service](/docs/otter/administration-maintenance/otter-installation-architecture-service) is hosted as, and doesn't have very many privileges by default.

## Configuring the Agent Listener in BuildMaster or Otter
In order for BuildMaster or Otter to be able to listen for outbound communication from your servers, you'll need to install a certificate that it can use for SSL/TLS-based communication. This certificate must be trusted by the servers that you will have connect to BuildMaster/Otter.

Creating this certificate is beyond the scope of this article, and if you're unsure of how to configure this, search for things like "Create and Install a Self-Signed SSL/TLS Certificate" to learn how to do this.

Once you've gotten a certificate, you need to configure the Agent Listener in BuildMaster or Otter. This can be done on the Admin > Agent Listener Dashboard page.

![](/resources/docs/otter-servers-configure-agent-listener.png)

Next, you'll need to create a server in BuildMaster or Otter that refers to the connecting agent. You can do this on the Servers > Add Server page; you'll now notice a new option for Inedo Agent (Outbound).

That page will provide a secret key that BuildMaster or Otter will use to identify the server. You need a different, unique key on each server that you want BuildMaster/Otter to communicate with, as this is what will identify one server from another.
