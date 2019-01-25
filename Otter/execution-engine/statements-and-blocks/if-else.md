---
title: If / Else Blocks
keywords: otter,execution-engine,operation-groups
sequence: 400
---

If / Else blocks allow for greater flexibility when creating plans, and reusing plan blocks in multiple situations.

For example, maybe a security certificate is only needed on production servers, so an If/Else block can be inserted so that if the server is in the Production environment, the security certificate will be installed via PowerShell.

<tab-block>
    <tab name="Visual Mode">
        <img class="screenshot" src="/resources/documentation/otter/if-else-block.png" alt="If Else Block" />
    </tab>
    <tab name="Text Mode (OtterScript)">
        <img class="screenshot" src="/resources/documentation/otter/if-else-text.png" alt="If Else Text" />
    </tab>
</tab-block>

If this is run on any non-production server, it will simply log that a certificate isn't needed.
