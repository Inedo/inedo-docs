---
title: "Client/Server Architecture"
order: 6
---

:::(Error) (Reference Documentation)
This documentation is intended to describe the inner workings of the Inedo Agent, and does not provide any guidance on configuration or troubleshooting.
:::


An Inedo Agent server has, at a minimum, the InedoAgent.exe process. This typically runs as a Windows service, although may be run interactively for diagnostic purposes.


This process is only responsible for listening for requests from agent clients (BuildMaster or Otter); as such, it consumes minimal system resources and should have a quick startup time.

The Inedo Agent listens for and receives individual messages from the network stack (protocol described separately); there two types of messages:

- [Meta](/docs/inedo-agent/internal-architecture/inedoagent-server-architecture-message-formats1) - the message should be processed by the InedoAgent.exe process directly
- Product - the message is passed along to the appropriate product host process

### Product Host Processes 

All orchestration happens in the context of a product host process. For Otter, this process is named Otter.Agent.exe. For BuildMaster, this process is named BuildMaster.Agent.exe.

These processes are only ever intended to be started from a running InedoAgent.exe process, and will terminate immediately if executed any other way. Once started, a product host process communicates with InedoAgent.exe using a shared-memory buffer; this is functionally similar to a pipe.

Once started, a product host process will load all of the appropriate product's extensions and wait for messages from the starting process (InedoAgent.exe).

### Sessions 

A *session* is essentially an identifier for a product host process instance, and all communication with a product
host process happens in the context of this session. Sessions have the following behaviors/properties:

- Fully isolated from InedoAgent.exe and from each other - no handles are inherited and the only communication channel is the shared memory buffer
- Maintains a list of remotely-opened files and remotely-launched processes
- When terminated gracefully, all file handles are closed
- When no clients have connected for 30 minutes, the session's product host process will be terminated
- In the event of an unexpected disconnect, the session will remain open for 30 minutes so a connection can be reestablished

### Messages 

A session operates on bi-directional, length-prefixed messages.

Messages sent to the server (i.e. a product to an agent) are either instructions to perform some sort of task, or a request for some kind of data. Messages sent to the client (i.e. an agent to a product) are generally data only.

#### *Message Targets* 

Server messages specify a target (either Meta, BuildMaster, or Otter), which is used by InedoAgent.exe to determine which component will process the message. Its either processed by InedoAgent.exe directly (Meta), or forwarded to a product host process for processing.

Client messages do not need to specify a target, as the target will already be part of an open session.

#### Message Types 

While all messages specify a message code that identifies the type of message (i.e. how it should be processed), each target can only process its own message types. As such, [Meta](/docs/inedo-agent/internal-architecture/inedoagent-server-architecture-message-formats1#meta)-targeted messages are part of the Inedo Agent specification, but product-targeted messages are not.

Any references to BuildMaster or Otter messages types are for example purposes only; they are not part of the agent specification, and are subject to change in any version of the product.

#### Message Responses 

Unlike HTTP/1.1 and similar unidirectional protocols, when all of the bytes representing a message are transmitted to the server, the client does not wait for the server to send back a bunch of bytes as a response. As such, there is no concept of a *response* in the Inedo Agent protocol. When we use that term, it is simply the plain-English word that describes the action taken when a message is received.

If a response to a message is required, the server will simply send a message to the client with a specific message type indicating that it is a response to a specific type of message. Generally, the message data payload will contain some kind of token, along with serialized data or a serialized unhandled exception.