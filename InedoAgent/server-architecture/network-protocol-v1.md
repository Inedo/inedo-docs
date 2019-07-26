---
title: Network Protocol (v1)
subtitle: Network protocol (v1)
sequence: 40
keywords: inedo, inedo agent, network protocol
show-headings-in-nav: true
---

This protocol is intended for use over bidirectional TCP.

### Handshake {#handshake data-title="handshake"}

Upon connection, a client submits the following 24-byte handshake to the server:

|    Byte Offset         | Type      | Description |
| ---------------------- |:---------|:-----------------------------------------------------------|
|`0`                     |`Guid`    | Protocol ID: 6CEBD24E-4AF7-4A13-B9AF-81D3DFC644BE          |
|`16`                    |`int32`   | Protocol Version: 1000 (currently hardcoded)               |
|`20`                    |`int32`   | Session ID                                                 |

If the protocol ID is not valid, the server will immediately disconnect. Otherwise, the server responds with:

|    Byte Offset         | Type       | Description                                                                                    |
| ---------------------- |:-----------|:-----------------------------------------------------------------------------------------------|
|`0`                     | `int8`     | Required encryption mode. May be: <ul> <li> 0: none</li> <li>1: SSL</li> <li>2: AES</li></ul>  |
|`1`                     | `int8[16]` | AES initialization vector if using AES encryption                                              |
|`17/1`                  | `int32`    | Session ID – may be same as requested but server is allowed to return a different one          |
|`21/5`                  | `int32`    | Server version - [major].[minor].[build].[revision] (assembly version of server)               |  

Note: AES initialization vector is only included if using AES encryption. {.info}

Note: Session ID may be used in a future version to support reconnecting after a dropped connection, but it is currently ignored. {.info}

Once connected, the server waits to receive a message from the connected client.

### Client to Server Message {#client-server data-title="Client to Server Message"}

All client to server messages take the following form:

|    Byte Offset         | Type     | Description                                                |
| ---------------------- |:---------|:-----------------------------------------------------------|
|`0`	                   |`int32`   | Data length (length of Data field in bytes)                |
|`4`                     |`int8`    | Agent type                                                 |
|`5`                     |`int32`   |Command                                                     |
|`9`	                   |`bytes`   |	Data                                                       |

When using no encryption or SSL encryption, messages are sent in this format over TCP directly. When using AES encryption, the message looks like this:

|    Byte Offset         | Type      | Description                                                                                     |
| ---------------------- |:----------|:------------------------------------------------------------------------------------------------|
|`0`                     |`int32`    |	Encrypted data length (length of full encrypted message, including every field after this one) |
|`4*`	                   |`int8[16]` |	Initialization vector for next message                                                         |
|`20*`	                 |`int8`     |	Agent type                                                                                     |
|`21*`	                 |`int32`    |	Command code                                                                                   |
|`25*`	                 |`bytes`	   | Data                                                                                            |

\*All fields after length are encrypted. Offsets are applicable after message has been decrypted

### Server to Client Message {#server-client data-title="Server to Client Message"}

The server can send messages back to the client, with the following form:

|    Byte Offset         | Type      | Description                                |
| ---------------------- |:----------|:-------------------------------------------|
|`0`                     |`int32`    |Data length (length of Data field in bytes) |
|`4`                     |`int32`	   |Command code                                |
|`5`                     |`bytes`	   |Data                                        |

When using no encryption or SSL encryption, messages are sent in this format over TCP directly. When using AES encryption, the message looks like this:

|    Byte Offset         | Type      | Description |
| ---------------------- |:----------|:----------------------------------------------------------------------------------------------|
|`0`	                   |`int32`	   |Encrypted data length (length of full encrypted message, including every field after this one) |
|`4*`	                   |`int8[16]` |Initialization vector for next message                                                         |
|`20*`	                 |`int32`	   |Command code                                                                                   |
|`24*`	                 |`bytes`	   |Data                                                                                           |

\*All fields after length are encrypted. Offsets are applicable after message has been decrypted
