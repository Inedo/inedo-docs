---
title: Localization
subtitle: Localization in ProGet
sequence: 600
keywords: proget, administration, localization, translations
---

Localization allows for text in ProGet to be translated into different languages. Currently only support for English (en-US), but the Japanese (ja-JP) translations are coming soon.  You can also create custom language translations that can be submitted to Inedo for offical language support.

# Changing Your Language{#change-language data-title="Change Your LAnguage"}

By default, ProGet will use the logged in user's current culture (if available) to select a user's language.  You can change this at any time by going to your user settings and selecting a different language.

# Updating Translations{#updating data-title="Update Translations"}

You can modify existing languages or create a custom language in ProGet.  To modify a system language, you will first need to make a clone of it and deactivate the system language.

## Using the Editor {#editor data-title="Using the Editor"}

ProGet includes a language editor to change different pieces of text directly in ProGet.  It also allows you to perform different actions on a language like editing translations, activate/deactivate a language, clone a language, and import/export a language as XML.  To use the language editor, navigate to _Administration -> Localization_.

### Dictionary Edit Mode {#edit-mode data-titel="Dictionary Edit Mode"}

Dictionary edit mode allows you to change translations on a language while using ProGet.  It will add an edit translation button on any text that can be translated.  You will simply hover over text that looks like {add description} and click edit.  This will then bring up the language editor and focused on the text you would like to change.

To enter Dictionary Edit Mode, navigate to the Language editor and click the `Enter Dictionary Edit Mode` button.  To leave Dictionary Edit Mode, navigate back to the Language Editor and click `Leave Dictionary Edit Mode`.

## Cloning a Languages{#clone data-title="Clone a Language"}

To clone a language, you will need navigate to the Localization editor by navigating to _Administration -> Localization_ and clicking clone.  To use the newly cloned language, you will need to [activate](#activate-deactivate) the language by clicking the `Activate` button at the top of the page.  If this culture exists on another language, activating this language will deactivate the existing language.

## Exporting and Importing Languages {#export-import data-title="Export/Import"}

You can export a translation as an XML file that can be modified in an editor of choice.  You can then re-import that language XML file back into ProGet to update a language.

## Activate/Deactivate a Language {#activate-deactivate data-title="Activate/Deactivate"}

Languages can be activated and deactivated.  Only one instance of a culture can be activated at a time.  If you try to activate a second instance of a culture, the previously activated culture will be deactivated.

# Submit New Translation to Inedo {#new data-title="Submit a New Translation"}

You can submit new languages to Inedo to be included as a system language.  To do this, first [export the language](#import-export) as an XML file.  Next, navigate to [my.inedo.com](https://my.inedo.com) and open a new support ticket.  Enter the subject as `[New Language] {culture being added}`, enter a brief message, and attach the exported Language XML.

# Submit an Update of a Translatiojn to Inedo {#change data-title="Submit a Update of a Translation"}

You can submit an update of a system languages to Inedo.  To do this, first [export the language](#import-export) with the change included as an XML file.  Next, navigate to [my.inedo.com](https://my.inedo.com) and open a new support ticket.  Enter the subject as `[Change Language] {culture being changed}`, enter a brief message including your what is changed, and attach the exported Language XML.

# Upgrades to ProGet and Languages {#updates data-title="Upgrading ProGet"}

When you upgrade ProGet, it will open any custom languages and remove any unused keys in that language.  ProGet always uses the system's en-US based language as a base.  Any keys missing from your custom language will be pulled from the en-US based system language.

