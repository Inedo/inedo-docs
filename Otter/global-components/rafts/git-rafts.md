---
title: Git-Based Rafts
subtitle: Storing Otter Plans and Configurations in Git
keywords: otter,rafts
sequence: 100
---

[Plans](/support/documentation/otter/core-concepts/plans) and [assets](/support/documentation/otter/core-concepts/assets) are essentially files, and Otter uses a [raft](/support/documentation/otter/global-components/rafts) to store them. Although rafts are an advanced feature, they are an integral part of how Otter works. The good news is, even if you don't need to use multiple rafts, you can simply replace the built-in raft ("*Default*") with a Git-based raft, and all your configuration plans, orchestration plans, and assets will be versioned.

Note: If you use GitHub, you will need to use a name/password if you want to push; otherwise, if you don't intend to save changes within Otter, use a read-only. {.info}

To do this, start by creating a repository on GitHub. You can name your repository virtually anything. For example, we'll name ours "initech-raft"

Then, in Otter, download the *Default* raft as a ZIP file by clicking *Administration* > *Rafts* then Download *Default*.

Copy the contents of the ZIP file directly to GitHub. Of course, you can always push them from the git client.

Go back to Otter, and delete the existing *Default* raft. Note that you can rename it to *DefaultOld* if you prefer not to delete.

Now we can create a Git raft named *Default* by clicking *Create Raft* > *Git*

Name your raft *Default*, and enter the Remote repository URL (click on *Clone or download* in your GitHub repository to find it) and your GitHub credentials. Then, enter *master* for "Branch" and Click *Save*

![](/resources/tutorials/otter-git/creategitraft.png){.screenshot}

To ensure that synchronization and storage were successful,

Click *Assets* > *Other Files* > *Default*

In our example, you'll see that the Accounts.zip file has been stored as an Asset, indicating that Otter was able to synchronize from Git.

![](/resources/tutorials/otter-git/assetsdefault.png){.screenshot}
