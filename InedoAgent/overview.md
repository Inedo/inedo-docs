---
title: What is the Inedo Agent?
sequence: 10

---
Our products (BuildMaster and Otter) automate and orchestrate your infrastructure by deploying files and sending commands to your servers. BuildMaster and Otter can communicate with servers using general-purpose channels like WMI (Windows) and SSH (Linux), or through a more light-weight and specialized protocol, the Inedo Agent.

To install the Inedo Agent, you can follow our [step-by-step guide for the GUI-based installer](/docs/inedoagent/installation/installation-guide), or use more advanced options like [silent installation](/docs/inedoagent/installation/silent-installation) or [manual installation](/docs/inedoagent/installation/manual).

## Why use The Inedo Agent?

Unlike general-purpose channels, the Inedo Agent was designed for the sole purpose of communicating with Inedo products. This offers several benefits, including:

* Flexibility; both Inbound- and Outbound communication is supported, which can greatly simplify router and firewall configuration across networks
* Easier to configure; just install and use without needing to configuring domain credentials or login certificates
* Performance; the communication protocol is highly-optimized, as it's only designed to work with our products
* Security; while all channels are encrypted, the Inedo Agent has a significantly lower attack surface than WMI or SSH, and can be configured with lower permissions

The main reason not to use the Inedo Agent is convenience. You may already have WMI or SSH enabled, and don't want to install an additional agent.

## Communication Modes

The Inedo Agent can operate in two different communication modes.

**Inbound Communication** is the default mode, and the Inedo Agent will listen for connections from product instances (BuildMaster, Otter). It’s okay to set up an agent in this mode without setting up the instance first, as nothing will connect to it.

**Outbound Communication** first requires that a product instance (BuildMaster, Otter) is configured to listen for communication, and that the instance is prepared to listen for connections from a specific agent. When installing the Inedo Agent, you’ll need to enter that instance’s network address (IP or host name) and SSL thumbprint, as well as the secret key to identify the server that’s connecting. When the Inedo Agent runs in Outbound Communication mode, it will immediately open a connection to the product instance it’s configured to connect to, and will indefinitely maintain that connection.

See [how to configure for outbound mode](https://docs.inedo.com/docs/inedoagent/configuration/outbound-mode) to learn more.
