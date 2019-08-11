---
title: Agent Installation Reference
keywords: otter
sequence: 200
---

<style>
    .documentation-content-wrapper .upcoming {
        background-color: #fff;
        position: relative;
    }
    .screenshot {
        opacity: .4;
    }
    .info.otter {
        border-color: #9163aa;
        color: #9163aa;
    }
    .documentation-content-wrapper .upcoming.buildmaster::before {
        content: ' -- Placeholder screenshot from BuildMaster -- ';
        background-color: #f15a24;
        z-index: 1;
        width: 100%;
        padding: 2px 4px;
        color: white;
        text-align: center;
    }
</style>

To facilitate communication between Otter and the Windows servers you want to configure and orchestrate, Otter uses a lightweight agent with a highly-optimized and resilient protocol. Installing agents is even easier than installing Otter, and the [Agent Installation Guide](/docs/various/inedo-agent/installation) will provide step-by-step instructions, as well as provide some detail as to what's happening behind the scenes.

:::info {.otter}
**Shared Documentation Note** - we've kept the [Agent Installation details](/docs/various/inedo-agent/installation) in the Various Documentation, see *Adding the Server to Otter* below:
:::

## Adding the Server to Otter {#adding-server}

Once the agent has been installed, the server must be added to Otter. Click the **Create Server** button on the **Servers** page, and enter the host name of the server, the port you installed the agent on, and the encryption key if you chose one.
