---
title: "Add Group Member"
order: 4
---

*Add Group Member* is available as a `pgutil` command and an HTTP Request, and will assign an existing User to an existing Group.

:::(Info) (🚀 Quick Example: Adding a User to a Group with pgutil)
This example adds the user `"jsmith"` to the group `Developers`:

```bash
pgutil security groups members add --member="jsmith" --name=Developers
```
:::

## Command Specification (CLI)
The `security groups members add` command is used to add an existing User to an existing Group.

The `--member` and `--name` options are always required.

**Adding a user to a group** requires the user name (e.g. `"jsmith"`) and group name (e.g. `Developers`):

```bash
pgutil security groups members add --member="jsmith" --name=Developers
```

Note source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification

To add a User to an existing Group, `POST` to the following URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a `SecurityGroup` (see [SecurityGroup.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityGroup.cs)) object as the request body.

```plaintext
POST /api/security/groups/update
```

To add a User, include their `username` in the `Users` array in the updated `SecurityGroup` object, along with all existing members. As this endpoint replaces the member list, the request body must contain the desired list of members.

## HTTP Response Specification

A `SecurityGroup` object will be returned on a successful `200`, indicating that the Group has been updated. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).

## Sample Scripts

The following scripts will import a CSV list of Users and add them to a specified Group in bulk. The CSV needs to formatted with the correct headers shown in this example:

```plaintext
User
jsmith
djones
mbrown
```

### Bulk Add Members to Group (PowerShell)
This script adds a list of users located in `C:\Inedo\users.csv` to the `Engineers` group.  

```powershell
$InstanceUrl = "https://proget.corp.local"
$ApiKey = "abc12345"
$GroupName = "Engineers"
$CsvPath = "C:\Inedo\users.csv"

$CsvUsers = Import-Csv $CsvPath | Select-Object -ExpandProperty User

if (-not $CsvUsers) {
    Write-Host "ERROR: CSV file contains no users."
    exit
}

$GroupsResponse = Invoke-RestMethod `
    -Method GET `
    -Uri "$InstanceUrl/api/security/groups/list" `
    -Headers @{ "X-ApiKey" = $ApiKey }

$Group = $GroupsResponse | Where-Object { $_.name -eq $GroupName }

if (-not $Group) {
    Write-Host "ERROR: Group '$GroupName' not found."
    exit
}

$currentMembers = $Group.users
if (-not $currentMembers) { $currentMembers = @() }

$UsersResponse = Invoke-RestMethod `
    -Method GET `
    -Uri "$InstanceUrl/api/security/users/list" `
    -Headers @{ "X-ApiKey" = $ApiKey }

$AllUsers = $UsersResponse.name

$ValidUsers = @()
$InvalidUsers = @()

foreach ($User in $CsvUsers) {
    if ($AllUsers -contains $User) {
        $ValidUsers += $User
    } else {
        $InvalidUsers += $User
    }
}

Write-Host "Valid users found in ProGet: $($ValidUsers -join ', ')"
if ($InvalidUsers.Count -gt 0) {
    Write-Host "WARNING: The following users do not exist in ProGet and will not be added:"
    Write-Host ($InvalidUsers -join ", ")
}

$UpdatedMembers = ($currentMembers + $ValidUsers) | Sort-Object -Unique

$Body = @{
    name = $GroupName
    users = $UpdatedMembers
} | ConvertTo-Json -Depth 10

$UpdateResponse = Invoke-RestMethod `
    -Method POST `
    -Uri "$InstanceUrl/api/security/groups/update" `
    -Headers @{ "X-ApiKey" = $ApiKey; "Content-Type" = "application/json" } `
    -Body $Body

Write-Host "Group update successful."

if ($InvalidUsers.Count -gt 0) {
    Write-Host "`nUsers NOT added (not found in ProGet):"
    Write-Host ($InvalidUsers -join ", ")
} else {
    Write-Host "`nAll CSV users were successfully added."
}
```

### Bulk Add Members to Group (Python)
This script adds a list of users located in `C:\Inedo\users.csv` to the `Engineers` group.

```python
import csv
import requests

INSTANCE_URL = "https://proget.corp.local"
API_KEY = "abc12345"
GROUP_NAME = "Engineers"
CSV_PATH = r"C:\Inedo\users.csv"

def get_groups():
    url = f"{INSTANCE_URL}/api/security/groups/list"
    headers = {"X-ApiKey": API_KEY}
    response = requests.get(url, headers=headers)
    response.raise_for_status()
    return response.json()

def get_users():
    url = f"{INSTANCE_URL}/api/security/users/list"
    headers = {"X-ApiKey": API_KEY}
    response = requests.get(url, headers=headers)
    response.raise_for_status()
    return response.json()

def update_group(members):
    url = f"{INSTANCE_URL}/api/security/groups/update"
    headers = {
        "X-ApiKey": API_KEY,
        "Content-Type": "application/json"
    }
    payload = {
        "name": GROUP_NAME,
        "users": members
    }

    response = requests.post(url, headers=headers, json=payload)
    response.raise_for_status()

    if not response.text.strip():
        return {"name": GROUP_NAME, "users": members}
    try:
        return response.json()
    except:
        return {"name": GROUP_NAME, "users": members}

csv_users = []
with open(CSV_PATH, newline="") as csvfile:
    reader = csv.DictReader(csvfile)
    for row in reader:
        csv_users.append(row["User"])

if not csv_users:
    print("ERROR: CSV contained no users.")
    exit(1)

groups = get_groups()
group = next((g for g in groups if g["name"] == GROUP_NAME), None)

if not group:
    print(f"ERROR: Group '{GROUP_NAME}' not found.")
    exit(1)

current_members = group.get("users", [])
all_users_response = get_users()
all_users = [u["name"] for u in all_users_response]
valid_users = [u for u in csv_users if u in all_users]
invalid_users = [u for u in csv_users if u not in all_users]

updated_members = sorted(set(current_members + valid_users))
update_response = update_group(updated_members)

print("\nGroup Update Complete")

if invalid_users:
    print("\nUsers NOT added (not found in ProGet):")
    print(", ".join(invalid_users))
else:
    print("\nAll CSV users were successfully added.")
```
