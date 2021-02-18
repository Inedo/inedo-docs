---
title: Server Flow
sequence: 30
---
:::attention {.technical}
**Reference Documentation.** This documentation is intended to describe the inner workings of the Inedo Agent, and does not provide any guidance on configuration or troubleshooting.
:::
This is the series of events which occurs when a client (product) connects to an Inedo Agent:

1. The client (BuildMaster or Otter) opens a TCP socket to the server (InedoAgent.exe) and sends a 24-byte Handshake message
2. The server sends a [Handshake](/docs/inedoagent/server-architecture/network-protocol) acknowledgement to the client
3. The server is now ready to receive a message from the client

The server will then wait for commands from the connected client. These connections are relatively short-lived, and kept open for the duration of deployment, configuration, or orchestration executions.

The following events occur when an InedoAgent.exe receives a message that targets a product host process:

1. InedoAgent.exe forwards message the product host process using the shared-memory buffer
2. The product host process reads the message and processes it
3. If the messages requires a response, the product host process sends a message to InedoAgent.exe using the shared-memory buffer
4. InedoAgent.exe forwards messages to connected clients

Also note that, while servers can have multiple connected clients, a client will generally share a single session across multiple executions.
