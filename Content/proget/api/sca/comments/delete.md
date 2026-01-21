---
title: "Delete Comment"
order: 3
---

*Delete Comment* is available as both a `pgutil` command and an HTTP Request, and will delete a comment.

:::(Info) (🚀 Quick Example: Deleting a comment with pgutil)
This example deletes a comment (`2`) in version `1.2.3` of a project named `myProject`:

```bash
pgutil builds comments delete --project=myProject --build=1.2.3 --number=2
```
:::

## Command Specification (CLI)
The `builds comments delete` command is used to delete a comment in a project's build.

The `--project`, `--build` and `--number` options are always required.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Deleting a comment** requires the project (e.g. `myProject`), the build version number (e.g. `1.2.3`), and the comment number (e.g. `2`)

```bash
pgutil builds comments delete --project=myProject --build=1.2.3 --number=2
```

## HTTP Request Specification
To delete a comment, simply `DELETE` to the URL with an [appropriate API Key](/docs/proget/api/sca#authentication).

```plaintext
DELETE /api/sca/comments?project=«projectName»&version=«releaseVersion»&number=«commentNumber»
```
## HTTP Response Specification

A successful `200` response will indicate the comment was successfully deleted. A `403` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/sca#authentication).

### Delete all comments (Powershell)
This script deletes all comments in version `1.2.3` of project `myProject`:

```powershell
$baseURL = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$projectName = "myProject"
$releaseVersion = "1.2.3"

$listEndpoint = "/api/sca/comments?project=$projectName&version=$releaseVersion"
function ListComments {
    $headers = @{
        "X-ApiKey" = $apiKey
    }
    return Invoke-RestMethod -Uri ($baseURL + $listEndpoint) -Headers $headers -Method Get
}
function DeleteComment($commentNumber) {
    $headers = @{
        "X-ApiKey" = $apiKey
    }
    $deleteEndpoint = "/api/sca/comments?project=$projectName&version=$releaseVersion&number=$commentNumber"
    return Invoke-RestMethod -Uri ($baseURL + $deleteEndpoint) -Headers $headers -Method Delete
}
$comments = ListComments
if ($comments.Count -gt 0) {
    Write-Host "Deleting all comments..."
    $comments | ForEach-Object {
        DeleteComment $_.number
        Write-Host "Comment number $($_.number) deleted."
    }
} else {
    Write-Host "No comments to delete."
}
```
