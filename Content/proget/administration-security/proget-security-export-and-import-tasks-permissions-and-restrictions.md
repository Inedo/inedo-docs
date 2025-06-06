---
title: "Export and Import Tasks, Permissions, and Restrictions"
order: 6
---

Starting with ProGet 6.0.6, you can export your tasks, permissions and restrictions and import them into another ProGet instance or update tasks and permissions in bulk.

## Export Tasks, Permissions, and Restrictions

You can export all the tasks and permissions of your instance as a JSON file. This includes the tasks, their attributes, user and group permissions, and user and group restrictions. After exporting, you can easily edit the file (add or remove tasks, attributes, permissions and restrictions) and import it.

To export your tasks, navigate to "Administration Overview" > "Manage Security" > "Tasks/Permissions" and select "Export Tasks" from the Test Privileges dropdown menu.

![export-tasks](/resources/docs/export-tasks.png){height="" width="50%"}

## Importing Tasks, Permissions, and Restrictions

The *Import* feature allows you to quickly configure or change ProGet's tasks, permissions, and restrictions multiple times by importing the JSON file exported from another instance of ProGet.

To import your tasks, navigate to "Administration Overview" > "Manage Security" > "Tasks/Permissions" and select "Import Tasks" from the Test Privileges dropdown menu.

The "Dry-run mode" option allows you to see the changes that *will* be made before you actively make the changes.

![import-tasks](/resources/docs/import-tasks.png){height="" width="50%"}

See [Security and Access Controls](/docs/proget/administration-security) for more information.