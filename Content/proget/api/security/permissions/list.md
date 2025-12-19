---
title: "List Permissions"
order: 3
---

*List Permissions* is available as both a `pgutil` command and an HTTP Request, and will return list describing all configured Permissions.

:::(Info) (🚀 Quick Example: Listing Permissions with pgutil)
This example lists all existing Permissions:

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

Note source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification
To list all Permissions, simply `GET` to the following URL with an [appropriate API Key](/docs/proget/api/security#authentication):

```plaintext
GET /api/security/permissions/list
```

## HTTP Response Specification

An array of `SecurityPermission` (see [SecurityPermission.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityPermission.cs)) objects will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).

## Sample Usage Scripts

The following scripts will export a complete list of granted and denied permissions as a `.csv` file. The `.csv` will be formatted with the headers shown in this example:

```sql
id,user,group,task,feedGroup,deny
2,,Administrators,Administer,,False
3,Anonymous,,Administer,,True
1,Anonymous,,View & Download Packages,,True
15,,Lead Developers,Publish Packages,,False
13,,Lead Developers,View & Download Packages,nugets,False
14,,Lead Developers,Promote Packages,nuget-feed,False
11,lcarter,,View & Download Packages,nugets,False
10,lcarter,,Manage Feed,nuget-feeds,False
12,jsmith,,View & Download Packages,nuget-feed,False

```

### Export Permissions List (PowerShell)
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

### Export Permissions List (Python)
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