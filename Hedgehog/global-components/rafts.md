---
title: Rafts
subtitle: Rafts in Hedgehog
sequence: 400
keywords: hedgehog, packages, rafts

---
:::attention{.technical}
![](/resources/images/icons/technical.png) **Rafts are an advanced feature:** If you are just getting started with Hedgehog, you should revisit this topic once you've familiarized yourself with the other components.
:::

Plans, assets, pipelines, and templates are essentially just files, and to make sharing, versioning, branching, etc. possible, Hedgehog uses a **raft** to store these files. A raft is sort of abstracted file system, specifically designed for storing these types of items.

## The Default Raft {#default-raft data-title="Default Raft"}

Because you likely won't need multiple rafts right away, a raft named "Default" is automatically created when you install Hedgehog. This is a Database raft and is backed up when you do a regular [Back-up of Hedgehog](/support/documentation/hedgehog/installation/backing-up).

If you only have a single raft configured, and that raft is named "Default", then the ability to filter or select rafts will not be exposed on plan, project, asset, etc. pages.

## Creating, Managing, and Downloading Rafts {#managing data-title="Creating, Managing, and Downloading Rafts"}

You can create, manage, and download a raft as a zip file by going to Admin > Global Components > Rafts.

### Rafts and Projects {#projects data-title="Rafts and Projects"}

When you specify a raft for a project, Hedgehog will always search within the associated raft for content, using the [Project Content Name Resolution](/support/documentation/hedgehog/bundling/projects/content-name) search. Only the associated raft will be searched, which means that if a parent project uses a different raft, content may never be located. If no raft is specified, then the "Default" raft (if one is named that) is used.

### Raft Repository Types {#repository data-title="Raft Repository Types"}

Rafts rely on an [Extensible Raft Provider](/support/documentation/hedgehog/administration/extensions) to retrieve and store raft data. There are three built-in raft types:

{.docs}
- **Database**: this is the default raft provider, and persists all information in Hedgehog's database; while the contents are not versioned, it's the simplest to use and back-up as part of the Hedgehog database
- **Disk**: the raft is persisted as a simple file structure on disk; this may be appropriate for quick testing purposes
- **Git**: this raft is synchronized with a branch of a remote Git repository.

## Disk Layout Conventions {disk-layout data-title="Disk Layout Conventions"}

When stored on disk, in source control, or exported as a zip file, rafts are laid-out as follows.

| <!-- -->    | <!-- -->    |
| ----        | ----        |
| Deployment Plans | Files (.otter) within the /plans subdirectory |
| Variables | An OtterScript stored in the /variables file, consisting entirely of assign variable statements |
| Scripts | Files (.ps1, .js, .sh) within the /scripts subdirectory |
| Modules | Files (.otter) within the /lib subdirectory |
| Assets | Files within the /files subdirectory |
| Pipelines | Files (.json) within the /pipelines subdirectory |
| Deployment set Templates | Files (.json) within the /deployment-set-templates subdirectory |
| Project-specific Content | A /projects subdirectory containing directories named by the project, and a layout identical to a raft |

### Project-specific Content {#project-specific data-title="Project-specific Content"}

Project-specific content is stored in directory named after the project in a /projects subdirectory. While this may make for some long paths, it allows for projects to be named anything. For example, the "default" pipeline in the BuildMaster > SDK project would be here: /projects/BuildMaster/projects/SDK/pipelines/default.json

### Raft Variables {#raft-variables data-title="Raft Variables"}

Variables persisted within a raft are not currently displayed anywhere in the UI, and are intended to be used for storing default or fallback values for plans stored in portable/reusable rafts. They are the lowest scope, and are only used if a [Configuration Variables](/support/documentation/hedgehog/deliver-deploy/configuration-variables) of the same name does not exist.

## Raft Asset Resolution {#raft-asset data-title="Raft Asset Resolution"}

Hedgehog can automatically resolve names using the [Project Content Name Resolution](/support/documentation/hedgehog/bundling/projects/content-name) system, sometimes it's convenient to specify an explicit, fully-qualified path. You can do this with a combination of raft names and Project Paths.

{.docs}
- **AssetName**: this uses project content name resolution, and searches the folder of the current project (if exists), and then up the projects chain to the root
- **RaftName::AssetName**: searches the root (only) of the specified raft
- **RaftName::ProjectPath::AssetName**: searches the project path (e.g. /buildmaster/sdk) only
