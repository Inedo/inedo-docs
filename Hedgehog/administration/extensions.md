---
title: Extending Hedgehog
subtitle: Extending Hedgehog
sequence: 500
keywords: otter, sdk

---
A lot of Hedgehog's functionality is delivered through extensions, a type of pluggable component library that provides both core capabilities as well as integrations with third-party tools. You can see what extensions are loaded, as well as install new, or upgrade extensions from the Admin > Extensions Page within your instance of Hedgehog.    

## Built-Extensions {#built-extensions data-title="Built-Extensions"}

Hedgehog ships with three required extensions: *InedoCore*, *Windows*, and *Linux*. When these are not installed, you will receive warnings in the UI, and the software will be missing core functionality.

## New Extensions and Updates {#extension-updates data-title="New Extensions and Updates"}

Hedgehog will automatically connect to the [inedo.com/den](/den), and notify you of newly available extensions and updates. You can download and install these from the same extensions overview page.

:::attention {.best-practice}
![](/resources/images/icons/best-practices.png){.screenshot}

A connection to inedo.com is recommended, but not required. If your Hedgehog instance cannot connect to inedo.com/den, then you will need to manually install and update extensions.
:::

## Creating Your Own Extension {#creating-extension data-title="Creating Your Own Extension"}

Although you can already do *anything* in Hedgehog with Script Assets and the other tightly-integrated PowerShell features, you can also build an *Hedgehog Extension* to add additional components and functionality into your Hedgehog installation.

A lot of Hedgehog's functionality is delivered through built-in extensions, and you can add several types of components by simply [creating an extension](/docs/inedosdk/extending/creating) that's built against the [Inedo SDK](/docs/inedosdk/overview). Here's what you can extend:

{.docs}
- **Operations** - executed during a deployment plan
- **Raft Repositories** - backing storage for plans and assets
- **Resource Credentials** - a secret
- **User Directories** - authentication and privileges
- **Variable Functions** - used in plans to provide or manipulate values

## Script Assets vs Custom Extensions {#script-vs-custom data-title="Script Assets vs Custom Extensions"}

Scripts are significantly easier to develop and manage: just write, test, then upload (or commit), and your script will be available for execution in a plan as any other operation. For most things you'd need to do in a plan, that's perfectly adequate.   

Extensions are a bit more involved, but allow you to build components other than operations, and provide maximum flexibility for building operations. You can also incorporate third-party libraries into your extension, and use the powerful libraries already built-in to the Inedo SDK.

{.docs}
- **Direct Agent Access** - with components from [Inedo.Agents](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Agents), you can build Operations that will be faster and scale better than using scripting runtime.
- **Rich Descriptions** - the [Inedo Documentation](https://inedo.com/support/sdk-reference/inedosdk/Inedo.Documentation) components enable you to describe Operations in a plan using a consistent, visually-rich description, so that anyone reading it will see, at-a-glance, what the operation is configured to do.

Additionally, extension may be easier to unit test and mock using the testing framework of your choice.
