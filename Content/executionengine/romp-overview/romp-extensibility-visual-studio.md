---
title: "Extensibility"
order: 6
---




::: (ERROR)
Romp for Visual Studio is currently a technical preview. You can [view the source code](https://github.com/Inedo/VsRomp) or [download the latest release](https://github.com/Inedo/VsRomp/releases) from GitHub to install in your instance of Visual Studio.
:::

*Romp for Visual Studio* is a plugin that makes it even easier to build Romp packages for your application components.

Just like the ProGet Visual Studio Plugin, this plugin adds an item to the project context menu. It’s a flyout menu named "Romp", with the following options:

![context-menu](/resources/docs/context-menu.png){height="" width=""}

### Configure

This command will open the Configure Project for Romp dialog, and displays a UI for the options available in `rompPackage.json`. If the file exists, the options will be pre-populated with those values.

When you click the Configure button, the configuration window appears, and a `.upack` folder will be created if it doesn't exist, while also updating the `rompPackage.json`.

As you tab through the configuration window, you'll be able to see where the edits made in the visual editor translate to the text files on disk.

#### Metadata

![metadata](/resources/docs/metadata.png){height="" width=""}

#### Package

![package](/resources/docs/package.png){height="" width=""}

#### Variables

![variables](/resources/docs/variables.png){height="" width=""}

#### Credentials

![credentials](/resources/docs/credentials.png){height="" width=""}

### Validate

This wraps the [romp validate](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-command-overview/romp-command-line-command-overview-installation#validate) method, and outputs any errors in the build output window.

### Package

This wraps the [romp pack](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-command-overview/romp-command-line-command-overview-creating-publishing#pack) method, outputting the package to the package root folder.

### Test

This runs the [romp install](/docs/executionengine/romp-overview/romp-cli-reference/romp-command-line-command-overview/romp-command-line-command-overview-installation#install) method, but specifies an option, which means it does not add the package to the local registry.

### Push

Similar to the ProGet Visual Studio Plugin.

## Other Notes

Romp for Visual Studio uses a `.upack/` folder, located at a project's root, that is laid out exactly like a [regular romp package](/docs/executionengine/romp-overview/romp-romp-packages), exception without a package/ subfolder. Instead, MSBuild's output folder is used to create the contents at packing time.

The plugin uses an embedded version of Romp (the version is indicated in the notes), but you can configure it to point to another installation on your machine if desired.