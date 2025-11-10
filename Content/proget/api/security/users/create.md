---
title: "Create User"
order: 1
---

*Create User* is available as both a `pgutil` command and an HTTP Request. It will create a new User account in ProGet, and return a [SecurityUser](/docs/proget/api/security#securityuser-object) object describing the new User created.

:::(Info) (🚀 Quick Example: Creating a new User account with pgutil)
This example creates the User `"John Smith"` with accompanying `displayname`, `email` and `password`

```bash
pgutil security users create --username="John Smith" --displayname=jsmith --email=jsmith@kramerica.com  --password=password123
```
:::

## Command Specification (CLI)
The `security users create` command is used to create a new User.

The `--username`, `--displayname`, and `--email` options are always required.

If `--password` is not specified, the User will be prompted to manually input a password and retype it to confirm. 

**Creating a user** requires the the user name (e.g. `"John Smith"`), display name (e.g. `jsmith`), and the email address (e.g `john@kramerica.com`). You can also include a password (e.g. `abc12345`).

```bash
pgutil security users create --username="John Smith" --displayname=jsmith --email=john@kramerica.com  --password=password123
```

## HTTP Request Specification
To create a new User account, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/security#authentication) and a [SecurityUser](/docs/proget/api/security#securityuser-object) object as the request body.

```plaintext
POST /api/security/users/add
```

## HTTP Response Specification

A successful (`200`) response body will contain a [SecurityUser](/docs/proget/api/security#securityuser-object) object. For example, to creating a new User account with the user name `"John Smith"`, the request would return this:

```json
POST /api/security/users/add

{
    "name": "John Smith",
    "displayName": "jsmith",
    "mail": "jsmith@kramerica.com",
    "password": "password123",
}
```

Note: When returned, the JSON object will hide the `password` field on output.

| Response | Details |
| --- | --- |
| **200 (Success)** | the user is created and the body will contain a [SecurityUser](/docs/proget/api/security#securityuser-object) object (with `password` hidden) |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [SecurityUser](/docs/proget/api/security#securityuser-object) object; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |

## Sample Usage Scripts

The following scripts will import a CSV of user information and create users in the specified ProGet instance in bulk. The CSV needs to formatted with the correct headers shown in this example:

```sql
Name,DisplayName,Email,Password
Alice Johnson,ajohnson,alice.johnson@example.com,Sunflower92!
Brian Lee,blee,brian.lee@example.com,MapleLeaf47#
Catherine Wu,cwu,catherine.wu@example.com,OceanWave88$
David Patel,dpatel,david.patel@example.com,StarryNight55@
Emily Torres,etorres,emily.torres@example.com,RiverStone31%
```

### Create Users (Powershell)
This PowerShell script create users in bulk based off the input `.csv` file located at `C:\Users\YourUser\Documents\users.csv` to a ProGet instance located at `proget.corp.local` with an API key `abc12345`:

```powershell
$csvPath = "C:\Users\YourUser\Documents\users.csv"
$hostname = "proget.corp.local"
$apiKey = "abc12345"

Import-Csv $csvPath | ForEach-Object {
    $body = $_ | Select-Object @{n='name';e={$_.Name}}, @{n='displayName';e={$_.DisplayName}}, @{n='email';e={$_.Email}}, @{n='password';e={$_.Password}} | ConvertTo-Json
    try {
        Invoke-RestMethod -Uri "https://$hostname/api/security/users/add" -Method Post -Headers @{ "X-ApiKey" = $apiKey; "Content-Type" = "application/json" } -Body $body
        Write-Host "Added: $($_.Name)"
    } catch { Write-Warning "Failed: $($_.Name) - $_" }
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
This Python script create users in bulk based off the input `.csv` file located at `C:\Users\YourUser\Documents\users.csv` to a ProGet instance located at `proget.corp.local` with an API key `abc12345`:

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
            print(f"Added: {row['Name']}")
        except requests.exceptions.RequestException as e:
            print(f"Failed: {row['Name']} - {e}")

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