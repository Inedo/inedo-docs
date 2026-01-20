---
title: "Add Permission"
order: 1
---

*Add Permission* is available as both a `pgutil` command and an HTTP Request, and will either grant or deny a Task/Permission to a User or Group.

:::(Info) (🚀 Quick Example: Adding a Permission with pgutil)
This example grants the `Developers` Group Permission to `View & Download Packages` from Feeds in the `Approved Package Feeds` Feed group:

```bash
pgutil security permissions add --task="View & Download Packages" --group=developers --feed-group="Approved Package Feeds"
```
:::

## Command Specification (CLI)
The `security permissions add` command is used to grant or deny a Task/Permission to a User or Group.

The `--task` option together with either the `--user` or `--group` options are always required.

The `--feed` and `--feed-group` options are optional, and will scope the permission to either a Feed or Feed group. If these are omitted, the Permission is applied globally. 

The `--deny` option is optional, and is used to set a "deny" Permission instead of a grant.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Granting global Permission to a User** requires the the Task name (e.g.`"View & Download Packages"`), and the User name (e.g. `"jsmith"`):

```bash
pgutil security permissions add --task="View & Download Packages" --user="jsmith"
```

**Granting feed level Permission to a Group** requires the the Task name (e.g.`"Promote Packages"`), the Group name (e.g. `Developers`) and the Feed name (e.g. `approved-nuget`):

```bash
pgutil security permissions add --task="Promote Packages" --group=Developers --feed=approved-nuget
```

**Denying feed level Permission to a Group** requires the the Task name (e.g.`"View & Download Packages"`), the Group name (e.g. `Developers`), the Feed name (e.g. `unapproved-nuget`) and the `--deny` option:

```bash
pgutil security permissions add --task="View & Download Packages" --group=Developers --feed=unapproved-nuget --deny
```

**Denying feed group level permission to a user** requires the the Task name (e.g.`"Manage Feed"`), the User name (e.g. `"djones`), the Feed group name (e.g. `production-packages`) and the `--deny` option:

```bash
pgutil security permissions add --task="Manage Feed" --name-"djones" --feed-group=production-packages
```

## HTTP Request Specification
To add a Permission, simply `POST` to the following URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a `SecurityPermission` (See [SecurityPermission.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityPermission.cs)) object as the request body:

```plaintext
POST /api/security/permissions/add
```

## HTTP Response Specification

A `SecurityPermission` object will be returned on a successful `200` response and indicates the Permission has been added. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).

## Sample Usage Scripts

### Assign Feed-Level Permissions to a Group (PowerShell)
This PowerShell script will add task permissions selected by the user to a group `Developers`for a feed `approved-nuget` to a ProGet instance `proget.corp.local` with the API key `abc12345`. You can select one or more task permissions:

```powershell
$hostname = "proget.corp.local"
$apiKey   = "abc12345"
$group = "Developers"
$feed  = "approved-nuget"

# To set a permission, remove the # symbol from the start of that line.
$tasks = @(
    # "Administer"
    # "Manage Feed"
    # "Manage Projects"
    # "Promote Packages"
    # "Publish Packages"
    # "View & Download Packages"
)

if (-not $tasks -or $tasks.Count -eq 0) {
    Write-Warning "No tasks selected. Please uncomment one or more tasks before running the script."
    return
}

$apiUrl = "https://$hostname/api/security/permissions/add"

foreach ($task in $tasks) {
    Write-Host "Adding permission: $task for group '$group' on feed '$feed'..."

    $body = @{
        task  = $task
        group = $group
        feed  = $feed
    } | ConvertTo-Json

    try {
        Invoke-RestMethod -Uri $apiUrl -Method Post -Headers @{
            "X-ApiKey" = $apiKey
            "Content-Type" = "application/json"
        } -Body $body

        Write-Host "✅ Successfully added: $task"
    } catch {
        Write-Warning "❌ Failed to add: $task - $_"
    }
}

Write-Host "`nAll selected permissions processed."
```

### Assign Feed Group-Level Permissions to a User (Python)
This PowerShell script will add task permissions selected by the user to a user `jsmith` for a feed group `nuget-feeds` to a ProGet instance `proget.corp.local` with the API key `abc12345`. You can select one or more task permissions:

```python
import requests

hostname = "proget.corp.local"
api_key = "abc12345"
user = "jsmith"
feedGroup = "nuget-feeds"

# To set a permission, remove the # symbol from the start of that line.
tasks = [
    # "Administer",
    # "Manage Feed",
    # "Manage Projects",
    # "Promote Packages",
    # "Publish Packages",
    # "View & Download Packages"
]

if not tasks:
    print("⚠️  No tasks selected. Please uncomment one or more tasks before running the script.")
    exit()

api_url = f"https://{hostname}/api/security/permissions/add"
headers = {"X-ApiKey": api_key, "Content-Type": "application/json"}

for task in tasks:
    print(f"Adding permission: {task} for user '{user}' on feedGroup '{feedGroup}'...")

    payload = {"task": task, "user": user, "feedGroup": feedGroup}

    try:
        response = requests.post(api_url, headers=headers, json=payload)
        response.raise_for_status()
        print(f"✅ Successfully added: {task}")
    except requests.exceptions.RequestException as e:
        print(f"❌ Failed to add: {task} - {e}")

print("\nAll selected permissions processed.")
```