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

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Resolving an issue** requires the project (e.g. `myProject`), the build version number (e.g. `1.2.3`), and the issue number (e.g. `4`):

```bash
pgutil builds issues resolve --project=myProject --build=1.2.3 --number=4
```

## HTTP Request Specification
To resolve an issue, simply `POST` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication), the project `name`, `version`, and issue `number`:

```plaintext
POST /api/sca/issues?project=«projectName»&version=«releaseVersion»&number=«issueNumber»&resolve=true
```

## HTTP Response Specification

A successful `200` response will indicate the issue was resolved. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication).

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
