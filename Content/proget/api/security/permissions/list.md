---
title: "List Permissions"
order: 3
---

*List Permissions* is available as both a `pgutil` command and an HTTP Request, and will return an array of [SecurityPermission](/docs/proget/api/security#securitypermission-object) objects describing all configured Permissions.

:::(Info) (🚀 Quick Example: Listing permissions with pgutil)
This example lists all existing permissions:

```bash
pgutil security permissions list
```
:::

## Command Specification (CLI)
The `security permissions list` command is used to list all existing Permissions.

**Listing all Permissions** requires no additional options:

```bash
pgutil security permissions list
```

Example output:

```yaml
1: allow View & Download Packages globally for user Anonymous
2: allow Administer globally for group Administrators
3: allow Administer globally for user Anonymous
...
```

## HTTP Request Specification
To list all Permissions, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
GET /api/security/permissions/list
```

## HTTP Response Specification

A successful (`200`) response body will contain an array of [SecurityPermission](/docs/proget/api/security#securitypermission-object) objects. For example, to listing all Permissions, the request would return this:

```json
GET /api/security/permissions/list

[
  {
    "id": 1,
    "group": "Administrators",
    "task": "Administer",
    "deny": false
  },
  {
    "id": 3,
    "user": "David Smith",
    "task": "View & Download Packages",
    "feed": "unapproved-nuget",
    "deny": true
  },
  {
    "id": 4,
    "group": "developers",
    "task": "View & Download Packages",
    "feedGroup": "production-packages",
    "deny": false,
  },
  {...}
]
```

| Response | Details |
| --- | --- |
| **200 (Success)** | body will contain an array of [SecurityPermission](/docs/proget/api/security#securitypermission-object) objects |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |

## Sample Usage Scripts

The following scripts will export a complete list of granted and denied permissions as a `.csv` file. The `.csv` will be formatted with the headers shown in this example:

```sql
id,user,group,task,feedGroup,deny
2,,Administrators,Administer,,False
3,Anonymous,,Administer,,True
1,Anonymous,,View & Download Packages,,True
15,,Lead Developers,Publish Packages,,False
13,,Lead Developers,View & Download Packages,nugets,False
14,,Lead Developers,Promote Packages,nugets,False
11,Liam Carter,,View & Download Packages,nugets,False
10,Liam Carter,,Manage Feed,nugets,False
12,Noah Davis,,View & Download Packages,nugets,False

```

### Create Users (PowerShell)
This PowerShell script will export a `.csv` file located at `C:\Users\YourUser\Documents\permissions_export.csv` from a ProGet instance located at `proget.corp.local` with an API key `abc12345`:

```powershell
$hostname = "proget.corp.local"
$apiKey = "abc12345"
$outputCsv = "C:\Users\YourUser\Documents\permissions_export.csv"

try {
    $permissions = Invoke-RestMethod -Uri "https://$hostname/api/security/permissions/list" -Method Get -Headers @{ "X-ApiKey" = $apiKey }
} catch {
    Write-Error "Failed to retrieve permissions: $_"
    exit
}

$csvData = $permissions | ForEach-Object {
    [PSCustomObject]@{
        id        = $_.id
        user      = if ($_.PSObject.Properties['user']) { $_.user } else { "" }
        group     = if ($_.PSObject.Properties['group']) { $_.group } else { "" }
        task      = if ($_.PSObject.Properties['task']) { $_.task } else { "" }
        feedGroup = if ($_.PSObject.Properties['feedGroup']) { $_.feedGroup } else { "" }
        deny      = if ($_.PSObject.Properties['deny']) { $_.deny } else { $false }
    }
}

try {
    $csvData | Export-Csv -Path $outputCsv -NoTypeInformation -Encoding UTF8
    Write-Host "Permissions exported successfully to $outputCsv"
} catch {
    Write-Error "Failed to export CSV: $_"
}
```

#### Example Output:

```powershell
Permissions exported successfully to C:\Users\YourUser\Documents\permissions_export.csv
```

### Create Users in Bulk (Python)
This Python script will export a `.csv` file located at `C:\Users\YourUser\Documents\users_export.csv` from a ProGet instance located at `proget.corp.local` with an API key `abc12345`:

```python
import csv
import requests

hostname = "proget.corp.local"
api_key = "abc12345"
output_csv = r"C:\Users\YourUser\Documents\users_export.csv"

api_url = f"https://{hostname}/api/security/permissions/list"
headers = {"X-ApiKey": api_key}

try:
    response = requests.get(api_url, headers=headers)
    response.raise_for_status()
    permissions = response.json()
except requests.exceptions.RequestException as e:
    print(f"Failed to retrieve permissions: {e}")
    exit()

with open(output_csv, mode='w', newline='', encoding='utf-8') as csvfile:
    fieldnames = ["id", "user", "group", "task", "feedGroup", "deny"]
    writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
    writer.writeheader()

    for perm in permissions:
        writer.writerow({
            "id": perm.get("id", ""),
            "user": perm.get("user", ""),
            "group": perm.get("group", ""),
            "task": perm.get("task", ""),
            "feedGroup": perm.get("feedGroup", ""),
            "deny": perm.get("deny", "")
        })

print(f"Permissions exported successfully to {output_csv}")
```

#### Example Output:

```python
Permissions exported successfully to C:\Users\YourUser\Documents\permissions_export.csv
```