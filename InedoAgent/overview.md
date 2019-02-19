---
title: Inedo Agent Overview
subtitle: What is the Inedo Agent
sequence: 10
keywords: inedo, inedo agent

---
The Inedo Agent is a lightweight Windows service that allows a server to be orchestrated using a connected instance of BuildMaster, Hedgehog, or Otter. Orchestration includes:

{.docs}
- File system access - ability to read and write files/directories
- Remote code execution - methods in .NET assemblies can be invoked in a remote context
- Remote process execution - executables can be launched with output streamed back

## General Architecture ##
The Inedo Agent follows a standard client-server architecture, although the terminology may seem a bit counterintuitive at first, because the Inedo Agent is actually operating as a server: it services multiple requests simultaneously from one or more clients.

BuildMaster and Otter products are the clients; using their service and web application, the products connect to an Inedo Agent installed on a server.
