---
title: Versions & Release Notes
sequence: 30
keywords: inedo, inedo agent, upgrading
---

Because the agent protocol requires that all Inedo Agent clients and servers be fully backward- and forward-compatible, having a semantic versioning scheme doesn't make a lot of sense. Instead, we have a single integer that we use to differentiate released versions.

Published versions:

| Version | Date | Shipped in Product | Notes |
| ------- |------| ------------------ |-------|
| [44](http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.44.exe) - major update | August 7, 2019 | BuildMaster 6.1.11 | [IAGT-10](https://inedo.myjetbrains.com/youtrack/issue/IAGT-18) - FIX: Instance ID is not always respected in agent commands |
| [43](http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.43.exe) - major update | July 19, 2019 | none yet | [IAGT-3](https://inedo.myjetbrains.com/youtrack/issue/IAGT-3) - FIX: Agent version in registry is not updated during automatic agent upgrades<br/>[IAGT-4](https://inedo.myjetbrains.com/youtrack/issue/IAGT-4) - Allow larger encryption keys for AES encryption mode<br/>[IAGT-9](https://inedo.myjetbrains.com/youtrack/issue/IAGT-9) - FIX: Installer should not change encryption key if the configuration already exists<br/>[IAGT-10](https://inedo.myjetbrains.com/youtrack/issue/IAGT-10) - Update Logo to use Inedo glyph instead of Otter glyph<br/>[IAGT-14](https://inedo.myjetbrains.com/youtrack/issue/IAGT-14) - Implement agent protocol v2<br/>[IAGT-15](https://inedo.myjetbrains.com/youtrack/issue/IAGT-15) - Add multi-instance support<br/>[IAGT-16](https://inedo.myjetbrains.com/youtrack/issue/IAGT-16) - Add isolation/impersonation support<br/>[IAGT-17](https://inedo.myjetbrains.com/youtrack/issue/IAGT-17) - FIX: Validate current agent name in .current file after it is read |
| [42](http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.42.exe) | March 4, 2019 | *BuildMaster 6.1.1* | [IAGT-12](https://inedo.myjetbrains.com/youtrack/issue/IAGT-12) - FIX: Unhandled exceptions during command deserialization can cause agent to crash |
| [41](http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.41.exe) | September 27, 2018 | *Otter 2.1.0* | [IAGT-5](https://inedo.myjetbrains.com/youtrack/issue/IAGT-5) - Target .NET 4.5.2<br/>[IAGT-6](https://inedo.myjetbrains.com/youtrack/issue/IAGT-6) - FIX: Handshake connection timeouts may not expire properly<br/>[IAGT-7](https://inedo.myjetbrains.com/youtrack/issue/IAGT-7) - Allow more than one client to handshake with the server at one time<br/>[IAGT-8](https://inedo.myjetbrains.com/youtrack/issue/IAGT-8) - Add HandshakeTimeout configuration value  |
| [40](http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.40.exe) | January 18, 2018 | Otter 2.0.2 | [IAGT-2](https://inedo.myjetbrains.com/youtrack/issue/IAGT-2) - FIX: Operations hang if agent process exits before they complete |
| [39](http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.39.exe) | November 8, 2018 | BuildMaster 5.8.2 | [IAGT-1](https://inedo.myjetbrains.com/youtrack/issue/IAGT-1) - FIX: Possible leaked memory mapped file pointer when resources are released via finalizer |
| [38](http://cdn.inedo.com/downloads/inedo-agent/InedoAgentSetup.38.exe) | August 14, 2017 | Otter 1.7.2<br/>BuildMaster 5.8.0 | Fix v37 regression (may crash after idle timeout period) |
| 37 | August 14, 2017 | Otter 1.7.1 | Potential fix for memory leaks |
| 36 | July 6, 2017 | BuildMaster 5.7.3<br/>Otter 1.6.3 | Agent installer change to better report errors |
| 35 | September 16, 2016 | BuildMaster 5.4.0<br/>Otter 1.4.1 | Add SessionID to error log |
| 34 | August 14, 2016 | BuildMaster 5.1.4<br/>Otter 1.2.0 | Major changes; BuildMaster support and various improvements including Healthcheck |
| 20* | May 24, 2016 | Otter 1.1.2 | Minor installer bugfix for silent installation |
| 17* | December 23, 2015 | Otter 1.0.0 | First public version |

The gap between versions is intentional. Most of our *released* versions are not publicly available, and are built and released for one or two users, to attempt to address/isolate some strange edge case.

Note that BuildMaster did not use the Inedo agents until v5.1.4. {.info}

*Note that versions 17 and 20 will still work, but only with Otter. {.info}

