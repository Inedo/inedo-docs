---
title: "Rafts & Git Storage"
order: 9
---

Otter uses a "raft" to store items like scripts, script assets, orchestration plans, and job templates.

A "raft" is like a drive or folder that stores these items as files. There are [different types of rafts](#raft-types), which means you can choose to store these items (files) within Otter itself, an external Git repository, or even in a readonly zip file.

You can also configure multiple rafts, which allows you to have some configuration stored within the Otter database, and have other configuration (such as common scripts or text files) shared with other instances of BuildMaster or Otter using a zip file or Git repository.

:::(Error)
**Rafts are an advanced feature.** If you are just getting started with Otter, you should revisit this topic once you've familiarized yourself with the other components.
:::

## The Default Raft

When you install Otter, a raft named "Default" is automatically created. This is a Database raft and is backed up when you do a regular [Back-up of Otter](/docs/otter/administration-maintenance/otter-installation-backing-up).

For most users, this initial configuration is totally sufficient; some may wish to replace this "Default" raft with another raft, such as a Git-based Raft.

If you only have a single raft configured, and that raft is named "Default", then the ability select rafts will not be exposed on script, server, job template, and other pages.

## Creating and Managing Rafts

You can create, manage, browse the contents of, and download a raft as a zip file by going to Admin > Raft Repositories.

Because rafts can be modified external to Otter, it's possible to create "corrupted" configuration, such as release template with invalid JSON. These will not be editable on the regular configuration pages (such as the release template editor), but if you browse to the files on the Raft Repositories page, you can edit the raw contents of the configuration as needed. 

### Exporting & Reusing Raft Configuration

One of the main benefits of downloading your raft as a zip file is that you can use it in a different instance of BuildMaster or Otter by using a zip file-based raft.

You could even use BuildMaster to automate this between instances; see our [CI/CD for Infrastructure & Configuration Management Guide](https://inedo.com/support/whitepapers/ci-cd-infrastructure-config-mgmt)

### Rafts and Servers/Roles 

[Server Roles](/docs/otter/connecting-to-your-servers-with-otter/otter-modeling-infrastructure-server-roles) may be associated with a raft. When a raft is specified, the corresponding configuration plan will be persisted within that raft. If no raft is specified, then the "Default" raft (if one is named that) is used.

Because rafts can be modified external to Otter, it's possible to create "orphaned" [Configuration Plans](/docs/otter/scripting-in-otter/otter-otterscript-and-operations#configuration) that are not associated with a server or server role in Otter. Those are simply ignored.

#### Referencing a Raft Asset in Otter Script

To referense scripts or other items stored in a raft, use `raftName::itemName`. For items stored in the `Default` raft, `Default::` is unecessary: if a raft name is not specified, a raft named `Default` will be assumed. 

## Raft Repository Types

Rafts rely on an [Extensible Raft Provider](/docs/otter/administration-maintenance/otter-administration-extensions) to retrieve and store raft data. There are three built-in raft types:

- **Database** - this is the default raft provider, and persists all information in Otter database; the contents are versioned, and it's the simplest to use and back-up as part of the Otter database
- **Disk** - the raft is persisted as a simple file structure on disk; there isn't much of a usecase for this type, so we don't really recommend using it for anything other than quick testing purposes
- **Zip file** - the raft is persisted in a zip file, and is read-only; this is an ideal way to manage configuration that is tested across environments; see our [CI/CD for Infrastructure & Configuration Management Guide](https://inedo.com/support/whitepapers/ci-cd-infrastructure-config-mgmt)
- **Git** - this raft is synchronized with a branch of a remote Git repository. See Git-Based Rafts to learn how to use this type of raft

### Tutorial: Move Your "Default" Raft to GitHub
Although rafts are an advanced feature, they are an integral part of how Otter works. The good news is, even if you don't need to use multiple rafts, you can simply replace the built-in raft ("*Default*") with a Git-based raft, and all your configuration assets will be versioned.

1. To do this, start by creating a repository on GitHub. You can name your repository virtually anything. For example, we'll name ours "initech-raft"

2. Then, in Otter, go to *Administration* > *Raft Repositories* and click the "download" link next to the raft named "Default".

3. Copy the contents of the ZIP file directly to GitHub. Of course, you can always push them from the git client.

4. Go back to Otter, and delete the existing "Default" raft. Note that you can rename it to *DefaultOld* if you prefer not to delete.

5. Create a Git raft named "Default" by clicking *Create Raft* > *Git*

6. Name your raft "Default", and enter the Remote repository URL (click on *Clone or download* in your GitHub repository to find it) and your GitHub credentials. Then, enter *master* for "Branch" and Click *Save*

7. To ensure that synchronization and storage were successful, click "browse"