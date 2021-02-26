---
title: Service Messenger
sequence: 900
keywords: otter, installation
---

The service messenger is a component of Otter responsible for lightweight nonessential communication between service (indexing) and web nodes. Specifically, it enables:

{.docs}
 - responsiveness, by allowing certain user-intitiated actions to trigger tasks in a service
 - diagnostics, by displaying the real-time status of Otter's service and its individual task schedulers

Otter will still function without the service messenger, but the scenarios above will be degraded.

The service messenger has two modes of operation: local named pipe or TCP. If you have a basic Otter installation with the web app and service on the same machine, then Otter will use a named pipe by default and no further configuration is necessary.

## Configuring for Load-balancing

First, make sure to follow the [instructions](/docs/Otter/installation/installation-guide/load-balanced) for configuring load-balancing.

Some additional configuration is required to enable the service messenger to work in a load balanced environment. Since the service and web apps are not on the same machine with this configuration, you will need to configure Otter to use TCP instead of a named pipe:

  1. Determine a TCP port to use for messenger traffic to/from service nodes (for example, 35845)
  2. Open this port in your firewall
  3. Create and configure an inbound rule for Windows Defender if applicable.
  4. Set the `Service.MessengerEndpoint` configuration value (Admin > Advanced Settings) to `tcp://<ServiceNodeHostName>:<Port>`
  5. Restart the service and all web nodes

## Configuring for High-availability

Like a simple load-balanced configuration, the service messenger must be configured to use TCP. Follow the same steps as configuration a load-balanced environment, except replace `<ServerNodeHostName>` in the `Service.MessengerEndpoint` configuration value with `*`. For example:

    tcp://*:<Port>

Otter will use the primary indexing node's IP address in place of the `*` as needed.

## Service Messenger Performance Impact and Limitations

To use the service messenger at all, web and service nodes must be accessible to each other either by be installed on the same machine or using a network. When using a network connection, the service node must have a dedicated port opened in its firewall for this purpose.

The performance impact of the service messenger on your network is negligible, as it is only used to transmit short (less than 1k typically) messages to a service node, and even this is performed only in response to certain user actions. The only page that transmits more information is the Service Status page in Otter, which streams log messages and task dispatcher statuses in real-time. You may observe high network traffic if many users have this page open simultaneously. However, this page is only accessible to administrators in a default installation.


## Security Considerations

Generally, no sensitive data is exchanged through the messenger channel. The only exception is any debug information written to a log that is displayed as live output on the service status page, and even this data is highly unlikely to contain anything sensitive. However, messages are encrypted using a AES256 key stored in the `Service.MessengerKey` configuration value as a base64 encoded string. This value is generated automatically on first use, but may be overwritten manually. Note that all service and web nodes must be manually restarted if this value is changed.

Aside from the encryption key, no authentication is performed. When using the messenger in TCP mode, we recommend to enabling IP whitelisting in your firewall to only allow connections from known web nodes as an additional security measure.
