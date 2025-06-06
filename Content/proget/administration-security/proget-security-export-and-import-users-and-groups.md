---
title: "Export and Import Users and Groups"
order: 6
---

Since ProGet 6.0.6, you can export your built-in users and import them into another ProGet instance. This makes sharing and maintaining your built-in users and groups much easier.

## Export Users & Groups

You can export all users and groups of your instance to a JSON file. This contains information about the users and an encrypted version of their password. After exporting, you can easily edit the file (add or remove users or groups) and import it. You can import these into BuildMaster and Otter as well.

To export your users and groups, navigate to "Administration Overview" > "Manage Security" > "Built-In Users & Groups" and select "Export Users & Groups" from the Export Users & Groups dropdown menu.

![export-users](/resources/docs/export-users.png){height="" width="50%"}

The `passwordBytes` and `saltBytes` can also be hidden by clicking the "Hide Password Bytes" button.

## Importing Users & Groups

The *Import* feature allows you to quickly configure ProGet's users and groups or make multiple changes by importing the JSON file exported from another instance of ProGet. In one of the next versions of BuildMaster and Otter you will be able to import users and groups into them as well.

To import your users and groups, navigate to "Administration Overview" > "Manage Security" > "Built-In Users & Groups" and select "Import Users & Groups" from the Export Users & Groups dropdown menu.

The "Dry-run mode" option allows you to see the changes that *will* be made before you actively make the changes.

![import-users](/resources/docs/import-users.png){height="" width="50%"}

### Updating passwords in bulk

You can also use the "Import Users And Groups" feature to update passwords in bulk.  To do this, simply remove the `passwordBytes` and `saltBytes` for the users you would like to update and replace it with `"password": "yourNewPassword"`.  

Example:
```json
{
  "users": [
    {
      "userName": "Admin",
      "displayName": "Administrator",
      "passwordBytes": "6R53UEz8LiA/yIyaWf0HAbc9zQ4=",
      "saltBytes": "sBaGqDLnTykFGQ==",
      "groups": [
        "Administrators"
      ]
    },
    {
        "userName": "jDoe",
        "displayName": "John Doe",
        "password": "MySecurePassword!",
        "groups": [
            "Developers"
        ]
    }
  ],
  "groups": [
    "Administrators",
    "Developers"
  ]
}
```

