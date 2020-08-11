---
title: Localization
subtitle: Localization in ProGet
sequence: 600
keywords: proget, administration, localization, translations
---

Localization allows for ProGet to be translated into your local language. Currently ProGet only supports English (en-US), but Japanese (ja-JP) is coming soon. You can also submit new language or modifications to existing language to Inedo to be included in ProGet's official language support.  Localization is available starting in ProGet 5.3.9.

# Changing Your Language{#change-language data-title="Change Your Language"}

By default, ProGet will use the server's current culture to select a user's default language. You can change this at any time by going to your user preferences in ProGet and selecting a different language. Simply click the user icon in the upper right corner and select _User Preferences_ from the dropdown menu.

*Note: User preferences are currently only available when pre-release features are enabled. Navigate to Administration -> Advanced Settings and enable the `Web.PrereleaseFeaturesEnabled` option.*

# Updating Translations{#updating data-title="Update Translations"}

ProGet supports adding a new language or modifying an existing language, allowing you to create a custom language.

::: {.attention .best-practice}
Custom languages are meant as a temporary means to create/modify languages with the intent to submit them to Inedo so they can be included in our official language support.
:::

To create/modify a language, you must:
1. [Clone a base language](#clone)
2. [Select that language](#change-language) as your language
3. Update the translation by one of the following options
  1. [Using the language editor](#editor)
  2. [Using Live Dictionary Edit Mode](#edit-mode)
  3. [Exporting and importing the language](#export-import)
4. When you're happy with your translation, [submit](#new) the XML file to Inedo

_Note: Creating/modifying languages are currently only available when pre-release features are enabled. Navigate to Administration -> Advanced Settings and enable the `Web.PrereleaseFeaturesEnabled` option.__

_Note: This feature is limited to users with Administrator permissions._

## Cloning a Language {#clone data-title="Cloning a Language"}

To clone a language, you will need navigate to _Administration -> Custom Languages_. Find the language you would like to base your translation on and click `clone`. To use the newly cloned language, you will need to select the language in your [User Preferences](#chang-language). Only one custom language per culture can exist and it will also take precedence over a system language with the same culture.

## Using the Editor {#editor data-title="Using the Editor"}

ProGet includes a language editor to change different pieces of text in ProGet. To use the language editor, navigate to _Administration -> Custom Languages_ and click on the 'edit' link next to language you would to edit.

_Note: System languages can not be directly edited. You will first need to [clone](#clone) the language prior to editing it._

### Live Dictionary Edit Mode {#edit-mode data-titel="Dictionary Edit Mode"}

Live Dictionary Edit Mode allows you to change translations directly in the UI while using ProGet. Text that can be translated will have a pencil icon next to it. When you hover over the text, the text will highlight and an `edit` link will appear. Click on the `edit` link to edit that translation.

To enter Live Dictionary Edit Mode, open your _User Preferences_ and enable `Live Dictionary Edit Mode`.

_Note: System languages can not be directly edited. You will first need to [clone](#clone) the language prior to editing it._

## Exporting and Importing Languages {#export-import data-title="Export/Import"}

You can export a translation as an XML file that can be modified in your editor of choice. You can then re-import that language XML file back into ProGet to update a language.

### Export a Language

You can export a language as XML by navigating to _Administration -> Custom Languages_ and clicking the `export` link to the right. 

### Import a Language

You can import a language XML by navigating to _Administration -> Custom Languages_ and clicking the `import` button.

## Submit a Translation to Inedo {#new data-title="Submit a Update of a Translation"}

You can submit a new language or an update of a system languages to Inedo. Follow these steps to submit a language:
1. [Export the language](#import-export) as an XML file.
2. [Submit a support ticket](https://my.inedo.com/tickets/new).
  1. Enter the subject as `[Add Language] {culture being added}` or `[Change Language] {culture being changed}` based on the modification you are submitting.
  2. Enter a brief message including what you have changed.
  3. Attach the exported language XML.

## Upgrades to ProGet and Languages {#updates data-title="Upgrading ProGet"}

When you upgrade ProGet, there is a chance that language keys have been removed, added, or updated. It is not recommended to use a custom languages longterm because of this. Keys that were removed from ProGet will be removed from your custom language next time a change is saved in the language editor (this includes updating values in Live Dictionary Edit Mode). ProGet always uses the en-US system language as a base, so any keys missing from your custom language will be pulled from the base language.

