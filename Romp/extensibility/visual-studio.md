---
title: Romp for Visual Studio
subtitle: Romp for Visual Studio
sequence: 770
keywords: romp, visual studio
---

::: attention {.technical}
Romp for Visual Studio is currently a technical preview. You can [view the source code](https://github.com/Inedo/VsRomp) or [download the latest release](https://github.com/Inedo/VsRomp/releases) from GitHub to install in your instance of Visual Studio.
:::

*Romp for Visual Studio* is a plugin that makes it even easier to build Romp packages for your application components.

Just like the ProGet Visual Studio Plugin, this plugin adds an item to the project context menu. It’s a flyout menu named "Romp", with the following options:

![](/resources/documentation/romp/visual-studio/context-menu.png)

### Configure {#configure data-title="Configure"}

This command will open the Configure Project for Romp dialog, and displays a UI for the options available in `rompPackage.json`. If the file exists, the options will be pre-populated with those values.

When you click the Configure button, the configuration window appears, and a `.upack` folder will be created if it doesn't exist, while also updating the `rompPackage.json`.

As you tab through the configuration window, you'll be able to see where the edits made in the visual editor translate to the text files on disk.

#### Metadata {#metadata data-title="Metadata"}

![](/resources/documentation/romp/visual-studio/metadata.png)

#### Package {#package data-title="Package"}

![](/resources/documentation/romp/visual-studio/package.png)

#### Variables {#variables data-title="Variables"}

![](/resources/documentation/romp/visual-studio/variables.png)

#### Credentials {#credentials data-title="Credentials"}

![](/resources/documentation/romp/visual-studio/credentials.png)

### Validate {#validate data-title="Validate"}

This wraps the [romp validates](/support/documentation/romp/command-line/command-overview/installation#validate) method, and outputs any errors in the build output window.

### Package {#package data-title="Package"}

This wraps the [romp pack](/support/documentation/romp/command-line/command-overview/creating-publishing#pack) method, outputting the package to the package root folder.

### Test {#test data-title="Test"}

This runs the [romp install](/support/documentation/romp/command-line/command-overview/installation#install) method, but specifies an option, which means it does not add the package to the local registry.

### Push {#push data-title="Push"}

Similar to the [ProGet Visual Studio Plugin](/support/tutorials/proget/push-to-proget-from-visual-studio).

## Other Notes {#other-notes data-title="Other Notes"}

Romp for Visual Studio uses a `.upack/` folder, located at a project's root, that is laid out exactly like a [regular romp package](/support/documentation/romp/romp-packages/layout), exception without a package/ subfolder. Instead, MSBuild's output folder is used to create the contents at packing time.

The plugin uses an embedded version of Romp (the version is indicated in the notes), but you can configure it to point to another installation on your machine if desired.
