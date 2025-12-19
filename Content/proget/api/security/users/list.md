---
title: "List Users"
order: 5
---

*List Users* is available as both a `pgutil` command and an HTTP Request, and will return a list of User accounts in ProGet.

:::(Info) (🚀 Quick Example: Listing User accounts with pgutil)
This example lists all existing Users:

```bash
pgutil security users list
```
:::

## Command Specification (CLI)
The `security users list` command is used to list all User accounts in ProGet.

The `--search-term` option is optional, and can be used to filter Users.

**Listing all existing users** requires no additional options:

```bash
pgutil security users list
```

Example output:

```plaintext
Admin (Administrator)
djones (David Jones)
jsmith (John Smith)
jbarnes (John Barnes)
```

**Listing users with a filter** requires a search term (e.g. `"John"`)

```bash
pgutil security users list --search-term="John"
```

Example output:

```plaintext
jsmith (John Smith)
jbarnes (John Barnes)
```

Note source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Request Specification
To list all User accounts in ProGet, simply `GET` to the following URL with an [appropriate API Key](/docs/proget/api/security#authentication):

```plaintext
GET /api/security/users/list
```

The list results can be filtered using the optional `search-term` query argument. 

```plaintext
GET /api/security/users/list?search-term=John
```

## HTTP Response Specification

An array of `SecurityUser` (see [SecurityUser.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityUser.cs)) objects will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).

## Sample Usage Scripts

The following scripts will export a complete list user information as a `.csv` file. The `.csv` will be formatted with the headers shown in this example:

```sql
Name,DisplayName,Email,Password
Alice Johnson,ajohnson,alice.johnson@example.com,Sunflower92!
Brian Lee,blee,brian.lee@example.com,MapleLeaf47#
Catherine Wu,cwu,catherine.wu@example.com,OceanWave88$
David Patel,dpatel,david.patel@example.com,StarryNight55@
Emily Torres,etorres,emily.torres@example.com,RiverStone31%
```

### Export Users (PowerShell)
This PowerShell script will export a `.csv` file located at `C:\Users\YourUser\Documents\users_export.csv` from a ProGet instance located at `proget.corp.local` with an API key `abc12345`:

```powershell
$hostname = "proget.corp.local"
$apiKey = "abc12345"
$outputCsv = "C:\Users\YourUser\Documents\users_export.csv"

try {
    $usersJson = Invoke-RestMethod -Uri "https://$hostname/api/security/users/list" -Method Get -Headers @{ "X-ApiKey" = $apiKey }
} catch {
    Write-Error "Failed to retrieve users: $_"
    exit
}

$csvData = $usersJson | ForEach-Object {
    [PSCustomObject]@{
        name        = $_.name
        displayName = $_.displayName
        email       = if ($_.PSObject.Properties['email']) { $_.email } else { "" }
        groups      = if ($_.PSObject.Properties['groups']) { ($_.groups -join ";") } else { "" }
    }
}

try {
    $csvData | Export-Csv -Path $outputCsv -NoTypeInformation -Encoding UTF8
    Write-Host "Users exported successfully to $outputCsv"
} catch {
    Write-Error "Failed to export CSV: $_"
}
```

#### Example Output:

```powershell
Users exported successfully to C:\Users\YourUser\Documents\users_export.csv
```

### Export Users (Python)
This Python script will export a `.csv` file located at `C:\Users\YourUser\Documents\users_export.csv` from a ProGet instance located at `proget.corp.local` with an API key `abc12345`:

```python
import csv
import requests

hostname = "proget.corp.local"
api_key = "abc12345"
output_csv = r"C:\Users\YourUser\Documents\users_export.csv"

api_url = f"https://{hostname}/api/security/users/list"
headers = {"X-ApiKey": api_key}

try:
    response = requests.get(api_url, headers=headers)
    response.raise_for_status()
    users = response.json()
except requests.exceptions.RequestException as e:
    print(f"Failed to retrieve users: {e}")
    exit()

with open(output_csv, mode='w', newline='', encoding='utf-8') as csvfile:
    fieldnames = ["name", "displayName", "email", "groups"]
    writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
    writer.writeheader()

    for user in users:
        writer.writerow({
            "name": user.get("name", ""),
            "displayName": user.get("displayName", ""),
            "email": user.get("email", ""),
            "groups": ";".join(user.get("groups", []))
        })

print(f"Users exported successfully to {output_csv}")
```

#### Example Output:

```python
Users exported successfully to C:\Users\YourUser\Documents\users_export.csv
```