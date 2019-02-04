---
title: Agent Improvement & Roadmap
subtitle: Agent Improvement & Roadmap
sequence: 60
keywords: inedo, inedo agent, roadmap
show-headings-in-nav: true
---
  We have several improvement ideas for the Inedo Agent, and are using this page to document them.

  Note that this used to be a page on our internal wiki, but as part of our [open initiative](/company/open-initiative), we're sharing them here. It's probably not very useful, and you probably won't get much out of reading them.

  ## Impersonation {#impersonation data-title="Impersonation"}

  Currently, all product host processes are run with the same user context as InedoAgent.exe. We should add the ability to impersonate a different user by extending the Open Session Request message.

  ## Multiple Agent Installation {#multiple data-title="Multiple Agent Installation"}

  It's currently a bit of a pain to install multiple agents; see [KB#1146](/support/kb/1146). It should probably only be supported with the manual install process, not the UI installer.

  ## Host Process Health Check {#host data-title="Host Process Health Check"}

  InedoAgent.exe can periodically query all host processes to make sure each is still handling messages properly. If a response isn't received in a reasonable amount of time, the process can be forcibly terminated. This will cause all sessions connected to it to fail, but at least it won't stay stuck.

  ## Improved Logging {#improved data-title="Improved Logging"}

  A diagnostic log file can already be configured for the InedoAgent, but it would be a lot more useful for troubleshooting
  if the meta-agent had a command to retrieve these so they are viewable from BuildMaster, Hedgehog, or Otter.

  ## Sentinels/Checksums {#sentinels data-title="Sentinels/Checksums"}

  Messages are just length-prefixed blocks with a simple sanity check on size. While the underlying TCP protocol guarantees correctness, these would be nice to add as an extra layer of protection in case of bugs in the agent protocol itself.

  ## More Encryption Options {#more data-title="More Encryption Options"}

  What we have is pretty good, but it certainly could be rounded out a little.

  ### AES {#aes data-title="AES"}

  Application-level symmetric AES encryption is the default mechanism for securing agent communications and verifying identity. Currently, this only allows a 128-bit key. We should use a larger key by default (probably 256-bit, for FIPS compliance).

  ### SSL {#ssl data-title="SSL"}

  Socket-level encryption has some performance issues. It seems prone to deadlock/freezing, but has not been widely adopted. It is primarily included because the old Inedo agent used this to encrypt its communications, and was provided here as an upgrade path.

  ### Asymmetric Key Exchange {#asymmetric data-title="Asymmetric Key Exchange"}

  This would be a good alternative to SSL/none. A thumbprint or certificate could be used to establish identity, but the communications would be protected by a symmetric key exchanged during the initial handshake using an asymmetric algorithm. Like SSH, this could still allow connecting without a thumbprint, but would be a warning/error condition. Ultimately this should be the preferred method instead of AES.
