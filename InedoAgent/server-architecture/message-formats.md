---
title: Message Formats
subtitle: Message Formats
sequence: 20
keywords: inedo, inedo agent, message formats
---

Meta-agent messages are backward and forward compatible, meaning that any changes to their formats must work in all past and future versions of the InedoAgent without error. In the event a breaking change is needed, the agent's protocol version number in the <a href="/support/documentation/inedoagent/server-architecture/network-protocol">handshake</a> will be incremented. Product-specific agent messages are allowed to change completely at any time, since agent host processes run in a totally isolated environment, and are always brought up to date with the connecting client version.

### Data Conventions ###
All messages are binary encoded. For clarity, high-level data types such as string, int32, etc are used to describe message formats. This section describes how those data types are serialized to binary.

{.docs}
- int32 - 32-bit integer. Encoded as 4 bytes, little-endian (least significant bits first).
- int64 - 64-bit integer. Encoded as 8 bytes, little-endian (least significant bits first).
- string - variable-length UTF8 string. The length of the string (in bytes) is prefixed as a variable-length unsigned 32-bit integer, written 7 bits at a time, with the high (8th) bit of each byte set to indicate another byte follows. For example, any length less than 128 is written as a single byte. This is the standard format used by the BinaryWriter class in .NET.
- array&lt;type&gt; - variable-length array of serialized item. The array is prefixed with an int32 specifying how many items follow.
- datetime - int64 value that stores a .NET UTC DateTime.Ticks value.

### Meta Messages ###
#### Update Agent (message code = 2) ####
Raw format for the message:

```
byte: Agent type to update (0=meta, 1=otter, 2=buildmaster, 3=hedgehog)
array<UpdateFile>: agent assembly files (binaries)
array<UpdateFile>: agent extension files (.upack files, only valid for agent type=otter/buildmaster/hedgehog)
```

Response for the message:
```
None
```

#### UpdateFile structure format ####

This is the format used to describe updated files in the Update Agent message:

```
string: file name
datetime: file modification timestamp
array<byte>: file contents
```

#### Get Agent Root Path (message code = 3) ####

Raw format for the message:
```
byte: Agent type (1=otter,2=buildmaster,3=hedgehog)
```

Response for the message:

```
string: Root temp path for the agent
```
