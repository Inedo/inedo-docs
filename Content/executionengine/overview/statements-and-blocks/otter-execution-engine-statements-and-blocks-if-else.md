---
title: "If / Else Blocks"
order: 8
---

If / Else blocks allow for greater flexibility when creating plans, and reusing plan blocks in multiple situations.

For example, maybe a security certificate is only needed on production servers, so an If/Else block can be inserted so that if the server is in the Production environment, the security certificate will be installed via PowerShell.

**Visual Mode**
![if-else-block.png](/resources/docs/if-else-block%282%29.png){height="" width=""}

**Text Mode**
![if-else-text.png](/resources/docs/if-else-text%281%29.png){height="" width=""}

If this is run on any non-production server, it will simply log that a certificate isn't needed.