---
title: "Delete Issue"
order: 2
---

*Delete Issue* is available as both a `pgutil` command and an HTTP Request, and will delete an Issue.

:::(Info) (🚀 Quick Example: Deleting an Issue with pgutil)
This example deletes issue `4` in build version `1.2.3` of a project `myProject`
```bash
pgutil builds issues delete --project=myProject --build=1.2.3 --number=4
```
:::

## Command Specification (CLI)
The `builds issues delete` command is used to delete an issue in a project's build.

The `--project`, `--build` and `--number` options are always required.

**Deleting an issue** requires the project (e.g. `myProject`), the build version number (e.g. `1.2.3`), and the issue number (e.g. `4`)

```bash
pgutil builds issues delete --project=myProject --build=1.2.3 --number=4
```

## HTTP Request Specification
To delete an issue, simply `DELETE` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```plaintext
DELETE /api/sca/issues?project=«projectName»&version=«releaseVersion»&number=«issueNumber»
```

**Deleting an issue** requires the project `name` (e.g. `myProject`), version (e.g. `1.2.3`), and issue `number` (e.g. `4`) :

```json
DELETE /api/sca/issues?project=myProject&version=1.2.3&number=4
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | indicates issue was deleted |
| **400 (Invalid Input)** | indicates invalid or missing arguments; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |

## Sample Usage Scripts

### Delete all issues (Powershell)
This script deletes all issues (resolved and unresolved) of version `1.2.3` of project `myProject`:

```powershell
$apiUrl = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$projectName = "Project"
$releaseVersion = "1.2.3"

$listApiUrl = "$apiUrl/api/sca/issues?project=$projectName&version=$releaseVersion"
$deleteApiUrl = "$apiUrl/api/sca/issues?project=$projectName&version=$releaseVersion&number="

$headers = @{
    "X-ApiKey" = $apiKey
}

function ListIssues {
    $response = Invoke-RestMethod -Uri $listApiUrl -Headers $headers -Method Get
    return $response
}

function DeleteIssue {
    param (
        [int]$issueNumber
    )
    $deleteUrl = $deleteApiUrl + $issueNumber
    Invoke-RestMethod -Uri $deleteUrl -Headers $headers -Method Delete
}

try {
    $issues = ListIssues

    foreach ($issue in $issues) {
        Write-Output "Deleting issue $($issue.number)..."
        DeleteIssue -issueNumber $issue.number
    }
    
    Write-Output "All issues deleted successfully."
}
catch {
    Write-Error "An error occurred: $_"
}
```