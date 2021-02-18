---
title: What is the Inedo Agent?
sequence: 10

---
The Inedo Agent is a lightweight Windows service that allows a server to be orchestrated using a connected instance of BuildMaster or Otter. Orchestration includes:

{.docs}
- File system access - ability to read and write files/directories
- Remote code execution - methods in .NET assemblies can be invoked in a remote context
- Remote process execution - executables can be launched with output streamed back

## Communication Modes

The Inedo Agent can operate in two different communication modes.

**Inbound Communication** is the default mode, and the Inedo Agent will listen for connections from product instances (BuildMaster, Otter). It’s okay to set up an agent in this mode without setting up the instance first, as nothing will connect to it.

**Outbound Communication** first requires that a product instance (BuildMaster, Otter) is configured to listen for communication, and that the instance is prepared to listen for connections from a specific agent. When installing the Inedo Agent, you’ll need to enter that instance’s network address (IP or host name) and SSL thumbprint, as well as the secret key to identify the server that’s connecting. When the Inedo Agent runs in Outbound Communication mode, it will immediately open a connection to the product instance it’s configured to connect to, and will indefinitely maintain that connection.