---
title: "Delete Comment"
order: 3
---

*Delete Comment* is available as both a `pgutil` command and an HTTP Request, and will delete a comment.

:::(Info) (🚀 Quick Example: Deleting a Comment with Curl)
This example deletes comment `1` in version `1.2.3` of a project named `myProject`, authenticating with the API key `abc12345`:

````
curl -X DELETE -H "X-ApiKey: abc12345" "https://proget.corp.local/api/sca/comments?project=myProject&version=1.2.3&number=1"
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `sca comments delete` command is coming soon. Similar to the HTTP Request, it will delete a comment.
:::

## HTTP Request Specification
To delete a comment, simply `DELETE` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication).

```
DELETE /api/sca/comments?project=«projectName»&version=«releaseVersion»&number=«commentNumber»
```

**Deleting a comment** requires the project `name` (e.g. `myProject`), `version` (e.g. `1.2.3`), and comment `number` (e.g. `1`)

```json
DELETE  /api/sca/comments?project=myProject&version=1.2.3&number=1
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | indicates the comment was successfully deleted |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [CommentInfo](/docs/proget/reference-api/proget-api-sca#commentinfo-object) object; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |

### Delete all comments (Powershell)
This script deletes all comments in version `1.2.3` of project `myProject`:

```powershell
$baseURL = "https://proget.corp.local"
$apiKey = "a1b2c3d4e5"
$projectName = "myProject"
$releaseVersion = "1.2.3"

$listEndpoint = "/api/sca/comments?project=$projectName&version=$releaseVersion"
$deleteEndpoint = "/api/sca/comments?project=$projectName&version=$releaseVersion&number=$commentNumber"

function ListComments {
    $headers = @{
        "X-ApiKey" = $apiKey
    }
    $response = Invoke-RestMethod -Uri ($baseURL + $listEndpoint) -Headers $headers -Method Get
    return $response
}

function DeleteComment($commentNumber) {
    $headers = @{
        "X-ApiKey" = $apiKey
    }
    $response = Invoke-RestMethod -Uri ($baseURL + $deleteEndpoint) -Headers $headers -Method Delete
    return $response
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