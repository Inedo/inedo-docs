---
title: "Create User"
order: 1
---

*Create User* is available as both a `pgutil` command and an HTTP Request, and will create a new User account in ProGet.

:::(Info) (🚀 Quick Example: Creating a new User account with pgutil)
This example creates the User `"djones"` with accompanying `displayname`, `email` and `password`

```bash
pgutil security users create --username="djones" --displayname="David Jones" --email=djones@kramerica.com  --password=password123
```
:::

## Command Specification (CLI)
The `security users create` command is used to create a new User.

The `--username`, `--displayname`, and `--email` options are always required.

If `--password` is not specified, the User will be prompted to manually input a password and retype it to confirm. 

**Creating a user** requires the the user name (e.g. `"djones"`), display name (e.g. `"David Jones""`), and the email address (e.g `djones@kramerica.com`). You can also include a password (e.g. `abc12345`).

```bash
pgutil security users create --username="djones" --displayname="David Jones" --email=djones@kramerica.com  --password=password123
```

## HTTP Request Specification
To create a new User account, simply `POST` to the following URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a `SecurityUser` object (see [SecurityUser.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/SecurityUser.cs)) object as the request body.

```plaintext
POST /api/security/users/add
```

Note source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.

## HTTP Response Specification

A `SecurityUser` object will be returned on a successful `200` response. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).

## Sample Usage Scripts

The following scripts will import a CSV of user information and create users in the specified ProGet instance in bulk. The CSV needs to formatted with the correct headers shown in this example:

```sql
Name,DisplayName,Email,Password
ajohnson,Alice Johnson,alice.johnson@example.com,Sunflower92!
blee,Brian Lee,brian.lee@example.com,MapleLeaf47#
cwu,Catherine Wu,catherine.wu@example.com,OceanWave88$
dpatel,David Patel,david.patel@example.com,StarryNight55@
etorres,Emily Torres,emily.torres@example.com,RiverStone31%
```

### Create Users (Powershell)
This PowerShell script create users from a `.csv` file located at `C:\Users\YourUser\Documents\users.csv` to a ProGet instance located at `proget.corp.local` with an API key `abc12345`:

```powershell
$csvPath = "C:\Users\YourUser\Documents\users.csv"
$hostname = "proget.corp.local"
$apiKey = "abc12345"

Import-Csv $csvPath | ForEach-Object {
    $body = $_ | Select-Object @{n='name';e={$_.Name}}, @{n='displayName';e={$_.DisplayName}}, @{n='email';e={$_.Email}}, @{n='password';e={$_.Password}} | ConvertTo-Json
    try {
        Invoke-RestMethod -Uri "https://$hostname/api/security/users/add" -Method Post -Headers @{ "X-ApiKey" = $apiKey; "Content-Type" = "application/json" } -Body $body
        Write-Host "Added: $($_.DisplayName)"
    } catch { Write-Warning "Failed: $($_.DisplayName) - $_" }
}

Write-Host "Done."
```

#### Example Output:

```powershell
Added: Alice Johnson
Added: Brian Lee
Added: Catherine Wu
Added: David Patel
Added: Emily Torres
Done.
```

### Create Users in Bulk (Python)
This Python script create users from a `.csv` file located at `C:\Users\YourUser\Documents\users.csv` to a ProGet instance located at `proget.corp.local` with an API key `abc12345`:

```python
import csv
import requests

csv_file_path = r"C:\Users\YourUser\Documents\users.csv"
hostname = "proget.corp.local"
api_key = "abc12345"

api_url = f"https://{hostname}/api/security/users/add"
headers = {
    "X-ApiKey": api_key,
    "Content-Type": "application/json"
}

with open(csv_file_path, newline='', encoding='utf-8') as csvfile:
    reader = csv.DictReader(csvfile)
    for row in reader:
        payload = {
            "name": row["Name"],
            "displayName": row["DisplayName"],
            "email": row["Email"],
            "password": row["Password"]
        }

        try:
            response = requests.post(api_url, headers=headers, json=payload)
            response.raise_for_status()
            print(f"Added: {row['DisplayName']}")
        except requests.exceptions.RequestException as e:
            print(f"Failed: {row['DisplayName']} - {e}")

print("Done.")
```

#### Example Output:

```python
Added: Alice Johnson
Added: Brian Lee
Added: Catherine Wu
Added: David Patel
Added: Emily Torres
Done.
```