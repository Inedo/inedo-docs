---
title: "Variable Prompts (Templates)"
order: 1
---

Variable Prompts help you create reusable [scripts](/docs/buildmaster/otterscript-execution-engine/buildmaster-scripts) and flexible [pipelines](/docs/buildmaster/deployment-continuous-delivery/buildmaster-pipelines) by prompting users for variable values at different parts of the process. These prompts include descriptions, help text, dropdowns, checkbox inputs, and more.

For example, when creating a new build, your pipeline can prompt `$RunSelenium`, `$EmbeddedKey`, and `$DatabaseVersion` and give the user guidance on what values to enter:

![buildmaster-pipeline-variables-prompts](/resources/docs/buildmaster-pipeline-variables-prompts.png){height="" width="50%"}

## Creating Variable Prompts
To add a variable prompt to your pipeline, click on "add prompt" under Variables & Prompts. You'll be presented with number of options that will control how the prompt behaves.

![buildmaster-pipeline-variables-prompt-edit](/resources/docs/buildmaster-pipeline-variables-prompt-edit.png){height="" width="50%"}

Depending on what you choose, different options will be displayed.

### Naming and Describing Variable Prompts
All variable prompts require at least a name. A description is recommended for checkboxes, or if the usage or purpose isn't obvious from the name.

 * **Name** will be displayed to users next to the input field and will also be used to access the value entered in your script. So, choose a name that makes sense to both users and in scripts, such as `RunSelenium`.
* **Description** is optional, and will be shown to users if they click on the help (`?`) button in the UI; for checkboxes, it will be displayed next to the checkbox.

### Deciding When to Prompt
The "Prompt at" option controls when users will be prompted to enter a value. 
* **Create Release** will prompt when a [release](/docs/buildmaster/modeling-your-applications/buildmaster-releases) is created and will create a release-scoped variable
* **Create Build** will prompt when a [build](/docs/buildmaster/builds-continuous-integration/buildmaster-builds) is created and will create a build-scoped variable
* **Deploy Build** will prompt when a build is deployed to the stages you specify and will create a deployment-scoped variable


If you select Deploy Build, you'll be presented with an additional option:
* **Prompt for stages** controls which stages the variable prompt will appear; if you enter no stages, then the prompt will appear in all stages at deploy time

If you select more than one option (such as Create Release and Create Build), users will be prompted for the same value at both times. Because of the way [variables cascade](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables), the build-scoped variable will override the value of the release-scoped variable.

If you don't select any options, the user will never be prompted. The only use case for this is to [customize the display of variables](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables/buildmaster-variables-customizing-display) that you create in a script using the `Set-BuildVariable` operation.

### Types of Variable Prompts
There are three basic types of variable prompts—Text, Checkbox, and List—each are described in more detail below. 

There will also be several dynamic prompt types, such as Servers, Environments, Server Roles, Applications, etc. These essentially list variables with values that you don't need to provide, such as the names of servers you've added to BuildMaster.

## Text Variable Prompts
Text variable prompts are displayed as a textbox. These will allow the user to enter a string of text before deploying a build and have a wide variety of uses, including version numbers, deployment file path, and URLs.

Selecting this option will present two options:
*  **Initial Value:** The default value that will be filled in if the user does not enter any text into the input field.
* **Required:** Specifies if the input is required or not. The script will not execute if a required value is not entered.
 
To make it as clear as possible what needs to be entered in this field, a description or initial value is recommended.

 ## Checkbox Variable Prompts
Checkbox variable prompts are displayed as a textbox and will either set a variable value or not. These are useful for controlling build and deployment options, such as whether certain tests should be run or whether a build is a release candidate.

Selecting this option will present two options:
  * **Initial Value:** Specifies if the checkbox will be ticked by default
 * **Value When Checked:** Specifies what variable value should be set when the box is ticked; this defaults to `true` but can be any value you'd like

A description is strongly recommended, as it will be displayed next to the checkbox as a label.

## List Variable Prompts
List variable prompts are the most complex type or prompts. They are displayed as either a dropdown list, a tag-selection list, or a regular textbox with autocomplete suggestions. 

There is a wide variety of uses cases, including:
* components of an application to deploy (Web, Database, Service)
* release type (Major, Minor, Beta)
* build configuration (Build, Release, «user entered»)

Selecting this option will present three list options:
* **List Values**; these will be the selectable options in the list (enter one per line)
* **Restrict value to items in list**; if not selected, the user will be able to enter their own value instead of being restricted to the List Values
* **Allow multiple items**;  if selected, the user will be allowed to select multiple List Values (or enter one or more of their own unless Restrict is selected)

The Restrict Values and Allow Multiple options control how the variable prompt is displayed:

| Display Type | Restrict Values | Allow Multiple |
| --- | --- | --- |
| Dropdown | ☑ | ☐ |
| Dropdown with Multi-select | ☑ | ☑ |
| Tag Selection List | ☐ | ☑ |
| Textbox with Autocomplete | ☐ | ☐ |

Regardless of the list options, there will be two other options:
*  **Initial Value:** The initial item(s) that will be selected; to specify multiple items, use the `@-variable` syntax (e.g. `@(Item1, Item2, Item3)`).
* **Required:** Specifies whether the input is required or not. The script will not execute if a required value is not entered.

When Allow Multiple is selected, the variable value will be an Array/List such as `@()` (for no items), `@(Item1)` for one item, or `@(Item1, Item2, Item3)` for multiple items.

Lists are generally preferred over textboxes as they show users which options they can choose. These are useful for controlling build and deployment options, such as if certain tests should be run, or if a build is a release candidate.

## Dynamic Prompts (Servers, Roles, Applications, Etc.)
Dynamic Prompts work just like List Variable prompts except they will use a data source for list items values instead of requiring you to manually configure the values in. Some dynamic prompt types will have additional configuration, such as Applications (which allows you to filter by Application Group), or Package Name (which requires a Feed Name).

BuildMaster includes several types of lists:

| Category | Available Prompts |
| -- | -- |
| Applications | Applications • Application Groups <br/><br/>*Applications can be filtered by an application group*  | 
| Infrastructure | Servers • Environments • Server Roles <br/><br/>*Servers can be filtered by an environment* |
| ProGet | Feed • Feed Type •  Package Name • Package Version  <br/><br/>*Feeds can be filtered by a feed type*<br/>*Package Name requires a feed* <br/> *Package Version requires a feed and package name* |

Dynamic Prompts are an extensible component, which means installing certain extensions will add more and you can [create your own using the SDK](/docs/inedosdk/extending-inedo-tools-using-the-sdk/inedosdk-extending-creating).


## Variable Display Options
You can control how a variable is displayed (rendered) on the Build Overview and Release Overview pages by using a variable prompt's Display Options.
* **Obscure the value of this variable from casual viewing** will display `(hidden)` instead of the value that was entered in the prompt
* **Customize the rendering (display) of the value of this variable** allows you to enter HTML to display the value however you'd like; see [Customizing how variables are displayed (rendered)](/docs/buildmaster/otterscript-execution-engine/buildmaster-variables/buildmaster-variables-customizing-display) to learn more

Note that the Obscure option is not intended as a security feature and you should not use this to store important secrets. Instead, you can use [Secure Credentials](/docs/buildmaster/configuring-for-your-team/buildmaster-administration-resource-credentials).