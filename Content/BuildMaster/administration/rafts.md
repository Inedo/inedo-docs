---
title: "Raft Repositories"
order: 2
---

:::(Error)
**Rafts are an advanced feature.** If you are just getting started with BuildMaster, you should revisit this topic once you've familiarized yourself with the other components
:::


Most of BuildMaster's configuration assets (scripts, pipelines, release templates, etc.) are stored as text files, and BuildMaster uses a raft repository to store these assets. 

A raft is sort of an abstracted file system, and there are [different types of rafts](#raft-types); this means that you can choose to store this configuration in the BuildMaster database, an external Git repository, or even in a read-only zip file.

You can also configure multiple rafts, which allows you to store some configurations in the BuildMaster database and share other configurations (such as common scripts or text files) with other instances of BuildMaster or Otter via a zip file or Git repository.

## The Default Raft

When you install BuildMaster, a raft named "Default" is automatically created. This is a Database raft and is backed up when you perform a regular [back-up of BuildMaster](/docs/installation/backing-up-restoring).

For most users, this initial configuration is quite sufficient; some may wish to replace the default with another raft, such as a Git-based raft.

If you have only one raft configured and that raft is named Default, the ability to select rafts will not appear on the Applications, Scripts, Pipelines, and other pages.

## Creating and Managing Rafts

You can create a raft, manage it, browse its contents, and download it as a .zip file by going to Admin > Raft Repositories.

Since rafts can be modified outside of BuildMaster, it is possible to create corrupted configurations, such as pipelines with an invalid JSON. These cannot be edited on the regular configuration pages (e.g. the pipeline editor), but if you access the files on the Raft Repositories page, you can edit the raw content of the configuration as needed.

### Importing a Raft
You can import a previously exported raft (or .zip file) into a database raft by clicking the Import Raft button on the Admin > Raft Repositories page. In this dialog box, specify the name of the new raft you want to import and the .zip file containing the archived raft you want to upload. Click Import and the new database raft will be created.

### Exporting & Reusing Raft Configuration

One of the main advantages of downloading your raft as a zip file is that you can use it in another instance of BuildMaster or Otter, using a zip file-based raft.

You could even use BuildMaster to automate this between instances; see our [CI/CD for Infrastructure & Configuration Management Guide](https://inedo.com/support/whitepapers/ci-cd-infrastructure-config-mgmt)

### Browsing a Raft
Click the Browse link for any raft on the Admin > Raft Repositories page to browse the contents of the raft. This provides access to the raw text-based configuration data contained in the raft and can be useful for advanced troubleshooting scenarios. You can make changes or deletions through this interface, but no validation is performed, so this should only be used to troubleshoot or fix invalid data.

### Rafts and Applications

An [Application](/docs/buildmaster/modeling-your-applications/buildmaster-applications-concepts) may be associated with a raft. If a raft is specified, the application's configuration (pipelines, release templates, etc.) is kept in that raft. If no raft is specified, the "Default" raft (if one exists) is used.

Application assets are stored in subfolders on a raft.  Assets are stored in the folder structure of `/projects/{application}/{asset type}/{asset}`.  This folder path structure exists for rafts configured at the server and application levels.

#### Referencing a Raft Asset in OtterScript

An asset can be referenced at the global level or at the application level. To reference a global asset in the default raft, simply use `global::itemName` or `Default::itemName`.  The use of `global::` is the same as using `Default::`.  If a global asset is stored in a raft other than the default raft, specify the raft name instead of `global` or `default` (for example, `raftName::itemName`).

An application can also reference an asset from another application.  To do this, use the `appName::itemName` syntax. To avoid ambiguity in naming, applications cannot be named _Global_, _Default_, or the same as a raft name.  Application naming always takes precedence when referencing raft assets, so applications named `Global`, `Default`, or the same name as a raft will cause unexpected references.

## Raft Repository Types

Rafts rely on an [Extensible Raft Provider](/docs/buildmaster/reference/extensions) to retrieve and store raft data. There are three built-in raft types:

- **Database** - this is the default raft provider and stores all information in the BuildMaster database; the content is versioned and is the easiest to use and save as part of the BuildMaster database
- **Disk** - the raft is persisted as a simple file structure on disk; there aren't many use cases for this type, so we don't really recommend using it for anything other than quick testing purposes
- **Zip file** - the raft is persisted in a .zip file and is read-only; this is an ideal way to manage configurations that are being tested in different environments; see our [CI/CD for Infrastructure & Configuration Management Guide](https://inedo.com/support/whitepapers/ci-cd-infrastructure-config-mgmt)
- **Git** - this raft is synchronized with a branch of a remote Git repository. See Git-based rafts to learn how to use this type of raft

### Tutorial: Move Your Default Raft to GitHub
Although rafts are an advanced feature, they're an integral part of how BuildMaster works. The good news is that even if you don't need to use multiple rafts, you can simply replace the built-in raft (*Default*) with a Git-based raft, and all your configuration assets will be versioned.

1. To do this, start by creating a repository on GitHub. You can name your repository virtually anything you want. For example, we named our repository "initech-raft"

2. Then go to *Administration* > *Raft Repositories* in BuildMaster and click on the download link next to the raft named Default

3. Copy the contents of the file .zip directly to GitHub. Of course, you can also push it via the Git client

4. Go back to BuildMaster, and delete the existing Default raft. Note that you can rename it to *DefaultOld* if you do not want to delete it

5. Create a Git raft named Default by clicking *Create Raft* > *Git*

6. Name your raft Default, and enter the URL of the remote repository (click *Clone or Download* in your GitHub repository to find it) and your GitHub credentials. Then enter *master* for Branch and click *Save*

7. To ensure that synchronization and storage were successful, click Browse
