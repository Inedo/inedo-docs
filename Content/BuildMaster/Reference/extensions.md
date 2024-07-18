---
title: "Extending BuildMaster"
order: 3
---

Much of BuildMaster's functionality is provided by extensions, a kind of pluggable component library that provides both core functionality and integrations with third-party tools. You can see which extensions are loaded, install new extensions, or update extensions on the *Admin* > *Extensions* page in your BuildMaster instance.

## Built-in Extensions

BuildMaster comes with the following extensions: *InedoCore*, *Windows*, *Scripting*, *Java*, *Bitbucket*, *DotNet*, *AzureDevOps*, *TeamCity*, *Python*, *Git*, *GitHub*, *GitLab*, and *Jenkins*. These are considered part of a BuildMaster release and are included in BuildMaster to enable instant integration with many commonly used services.

::: (INFO)
Extensions provide operations and integration points with various services. Even if you do not use an extension, it does not have any negative impact if you have it installed. For this reason, and to simplify installations and upgrades, we do not allow you to delete built-in extensions via the *Admin* > *Extensions* page. However, built-in extensions can still be upgraded individually.
:::
## New Extensions and Updates 

BuildMaster will automatically connect to [Extensions Feed](https://proget.inedo.com/feeds/Extensions) and notify you of newly available extensions and updates. You can download and install these from the same extensions overview page.

::: (INFO)
A connection to inedo.com is recommended, but not required. If your BuildMaster instance cannot connect to proget.inedo.com, then you will need to [install and update extensions manually](/docs/buildmaster/reference/extensions#manual-install).
:::

## Creating Your Own Extensions 

You can extend BuildMaster's functionality by [creating an extension](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating) that's built against the [Inedo SDK](/docs/inedosdk). This extension can also be used in Inedo's other tools. Here's what you can extend in BuildMaster:

| Name | Description |
| ---- | ----------- |
| Secure Credential | Used to represent logins and access keys that contain secrets (i.e. encrypted fields visible only to privileged users) |
| Secure Resource | Information used to provide access a specific resource (e.g., a Git repository) that may be associated with a Secure Credential |
| Operation | Used in an OtterScript plan to perform a higher-level automation task, such as collecting information about a server, deploying files to a server, or ensuring a particular set of configurations |
| Raft Repository | A self-contained abstract file system that is used as a back-up storage for plans, assets, and other files |
| User Directory | Responsible for authenticating users and groups. The default implementation uses users and groups stored in the product's database. |
| Variable Function | A function with zero or more input parameters that returns a value when evaluated by the execution engine, and often appears to be a built-in variable |
| Issue Source | Provides a filtered list of issues from an external source, such as Jira, for a given release context |
| Repository Monitors | Allows BuildMaster to monitor the state of a remote source code repository to perform tasks such as triggering new builds |


## Script  vs. Custom Extensions 

Scripts are much easier to develop and manage: just write, test, and upload, and your script is ready to run like any other operation in a schedule. For most things you need to run in a plan, this is perfectly sufficient.

Extensions are a bit more involved, but allow you to build components other than operations, and provide maximum flexibility for build operations. You can also include third-party libraries in your extension and take advantage of the powerful libraries that are already built into the Inedo SDK.

 - **Direct Agent Access** - with components from [Inedo.Agents](https://sdkdocs.inedo.com/sdk/inedosdk/inedo.agents), you can build Operations that are faster and scale better than using scripting runtime.
 - **Rich Descriptions** - the [Inedo.Documentation](https://sdkdocs.inedo.com/sdk/inedosdk/inedo.documentation) components enable you to describe Operations in a plan using a consistent, visually-rich description so that anyone reading it will see, at a glance, what the operation is configured to do.

In addition, it may be easier to test and simulate extensions using the testing framework of your choice.

## Extension Installation/Upgrade Options

### Manual Installation

For installations where access to the Internet is restricted, extensions can also be installed manually.

### Default Extension Installation

If your product installation can access the internet (specifically the https://proget.inedo.com/upack/Extensions URL), simply navigate to *Admin* > *Extensions*  to browse and install extensions.

### Previous Version Installation
Navigate to *Admin* > *Extensions* and click the extension you would like to roll back. Click *Change Version* in the dropdown list under the *Delete Extension* button.

### Upload Custom Extension
Navigate to *Admin* > *Extensions* and click the *Upload Custom Extension* button at the bottom of the page.

### Pre-Release Extensions
By default, BuildMaster is configured to use Inedo's Production Extension Feed, which contains only stable extension releases. If you want to use the pre-release feed instead (for example, if you are advised to try a pre-release extension to resolve an issue ), click the *Change Settings* button on the *Admin* > *Extensions* page and select *Inedo's pre-release feed* as the source for the extension.
