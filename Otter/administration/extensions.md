---
title: Extending Otter
subtitle: Extending Otter
sequence: 500
keywords: otter,sdk
show-headings-in-nav: true
---

A lot of Otter's functionality is delivered through extensions, a type of pluggable component library that provides both core capabilities as well as integrations with third-party tools. You can see what extensions are loaded, as well as install new, or upgrade extensions from the Admin > Extensions Page within your instance of Otter.

## Built-In Extensions {#built-in data-title="Built-In Extensions"}

Otter ships with three required extensions: *InedoCore*, *Windows*, and *Linux*. When these are not installed, you will receive warnings in the UI, and the software will be missing core functionality.

## New Extensions and Updates {#updates data-title="Extension Updates"}

Otter will automatically connect to [inedo.com/den](/den), and notify you of newly available extensions and updates. You can download and install these from the same extensions overview page.

:::attention {.best-practice}
![](/resources/images/icons/best-practices.png)

A connection to inedo.com is recommended, but not required. If your Otter instance cannot connect to inedo.com/den, then you will need to manually install and update extensions.
:::

## Creating Your Own Extension {#creating data-title="Creating an Extension"}

Although you can already do *anything* in Otter with [Script Assets] and the other tightly-integrated [PowerShell features], you can also build an *Otter Extension* to add additional components and functionality into your Otter installation.

A lot of Otter's functionality is delivered through built-in extensions, and you can add several types of components by simply [creating an extension] that's built against the [Inedo SDK]. Here's what you can extend:

{.docs}
- **Configurations** - information collected from a server, or a template for desired configuration
- **Operations** - be it a simple Execute operation, or an Ensure operation
- **Raft Repositories** - backing store for plans and assets
- **Resource Credentials** - a secret
- **User Directories** - authentication and privileges
- **Variable Functions** - used in plans to provide or manipulate values

## Script Assets vs Custom Extensions

Scripts are significantly easier to develop and manage: just write, test, then upload (or commit), and your script will be available for execution in a plan as any other operation. For most things you'd need to do in a plan, that's perfectly adequate.

Extensions are a bit more involved, but allow you to build components other than operations, and provide maximum flexibility for build operations. You can also incorporate third-party libraries into your extension, and use the powerful libraries already built-in to the Inedo SDK.

{.docs}
- **Direct Agent Access** - with components from [Inedo.Agents], you can build Operations that will be faster and scale better than using scripting runtime.
- **Rich Descriptions** - the [Inedo.Documentation] components enable you to describe Operations in a plan using a consistent, visually-rich description so that anyone reading it will see, at-a-glance, what the operation is configured to do.

Additionally, extension may be easier to unit test and mock using the testing framework of your choice.

## Extensions in Otter v1 {#extension-v1 data-title="Legacy Extensions"}

If you'd like to create an extension that supports Otter 1.7 or earlier, you'll need to use the Otter SDK. Although extensions created with the Otter SDK are not compatible with Otter 2.0 and later, they are managed in the same way (under Admin > Extensions).

Creating an extension is quite similar: just create a .NET 4.5 project that references the Inedo.Otter.SDK NuGet package from nuget.org and implement the desired extensibility points.

See the [Otter v1 SDK Reference] for more information.

To deploy the extension to Otter v1, add the compiled extension .dll to a zip file of the same name, with the extension .otterx. note that the .otterx file must have the same file name as the primary assembly it contains; for example, MyExtension.otterx must contain MyExtension.dll

You can verify that your extension has loaded correctly by viewing the Extensions Overview page from within the Otter v1 Administration section. From there, you will see all the extensions that are loaded in Otter v1, and the services they provide.

[Script Assets]: /docs/otter/core-concepts/assets
[Powershell features]: /docs/otter/modeling-infrastructure/powershell
[creating an extension]: /docs/various/inedo-sdk/creating
[Inedo SDK]: /docs/various/inedo-sdk/the-sdk
[Inedo.Agents]: /support/sdk-reference/inedosdk/Inedo.Agents
[Inedo.Documentation]: /support/sdk-reference/inedosdk/Inedo.Documentation
[Otter v1 SDK Reference]: /support/sdk-reference/otter
