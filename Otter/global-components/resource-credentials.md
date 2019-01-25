---
title: Resource Credentials
keywords: otter,executions
---

Otter can manage, store, and control access to tokens, passwords, API keys, or any of the other secrets needed for automating modern infrastructure with the use of Resource Credentials.

You can associate different credentials with different [Environments](../modeling-infrastructure/environments), and permit or restrict different [Users](/support/documentation/otter/administration/security) from managing or viewing the actual passwords for credentials. Of course, credentials are always encrypted before being stored.

There are several built-in types, and new types may be added with an [Extension](../administration/extensions):

{.docs}
- **Private Key** - a private key with an optional username and passphrase
- **BuildMaster** - a [connection to the BuildMaster](/support/tutorials/buildmaster/utilizing-infrastructure-sync) API
- **ProGet** - a connection to a [ProGet server](/proget)
- **Username & Password** - just a simple username and password, such as a Windows domain account

## Example: IIS Application Pool Credentials

In Microsoft's IIS, an Application Pool may be configured to run under a certain user account. Obviously, to do that, you must supply that username and password. Both of these fields are available on the [Ensure App Pool](../reference/operations/iis/ensure-app-pool) operation, but that means you would need to have those values be stored in plain text, in your [Plan](/support/documentation/otter/core-concepts/plans).

![Storing a Credential in Otter](/resources/documentation/otter/resource-3.png){.screenshot}

Alternatively, you could create a Username & Password credential...

![Storing a Credential in Otter](/resources/documentation/otter/resource-1.png){.screenshot}

... and then specify that, for the Otter credentials property.

![Using a Credential in Otter](/resources/documentation/otter/resource-2.png){.screenshot}


Otter will automatically map the appropriate fields at configuration time, allowing you to give the team viability into which credentials are being used, without sharing the actual credentials themselves.
