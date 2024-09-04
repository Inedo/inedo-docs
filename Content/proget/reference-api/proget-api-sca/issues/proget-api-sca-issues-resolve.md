---
title: "Resolve Issue"
order: 3
---

*Resolve Issue* is available as both a `pgutil` command and an HTTP Request, and will set the status of an Issue to "Resolved".

:::(Info) (🚀 Quick Example: Resolving an Issue with pgutil)
This example resolves issue `4` in build version `1.2.3` of a project `myProject`

```bash
pgutil builds issues resolve --project=myProject --build=1.2.3 --number=4
```
:::

## Command Specification (CLI)
The `builds issues resolve` command is used to resolve an issue in a project's build.

The `--project`, `--build` and `--number` options are always required.

**Resolving an issue** requires the project (e.g. `myProject`), the build version number (e.g. `1.2.3`), and the issue number (e.g. `4`)

```bash
pgutil builds issues resolve --project=myProject --build=1.2.3 --number=4
```

## HTTP Request Specification
To resolve an issue, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```plaintext
POST /api/sca/issues?project=«projectName»&version=«releaseVersion»&number=«issueNumber»&resolve=true
```

**Setting an issue to resolved** requires the project `name` (e.g. `myProject`), `version` (e.g. `1.2.3`), and issue `number` (e.g. `2`):

```json
POST /api/sca/issues?project=myProject&version=1.2.3&number=2&resolve=true
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | indicates issue was resolved |
| **400 (Invalid Input)** | indicates invalid or missing arguments; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error |

### Resolve all issues (Powershell)
This script resolves all issues of version `1.2.3` of project `myProject`:

```powershell
$BaseUrl = "https://proget.corp.local"
$ProjectName = "myProject"
$ReleaseVersion = "1.2.3"
$ApiKey = "a1b2c3d4e5"

$ListApiUrl = "$BaseUrl/api/sca/issues?project=$ProjectName&version=$ReleaseVersion"
$ResolveApiUrl = "$BaseUrl/api/sca/issues?project=$ProjectName&version=$ReleaseVersion&number={0}&resolve=true"

$Headers = @{
    "X-ApiKey" = $ApiKey
}

function ListIssues {
    $response = Invoke-RestMethod -Uri $ListApiUrl -Headers $Headers -Method Get
    return $response
}

function ResolveIssue($issueNumber) {
    $url = $ResolveApiUrl -f $issueNumber
    $response = Invoke-RestMethod -Uri $url -Headers $Headers -Method Post
    return $response
}

$issues = ListIssues

if ($issues) {
    Write-Host "Resolving all issues..."
    foreach ($issue in $issues) {
        ResolveIssue $issue.number
        Write-Host "Issue $($issue.number) resolved."
    }
} else {
    Write-Host "No issues found."
}
```