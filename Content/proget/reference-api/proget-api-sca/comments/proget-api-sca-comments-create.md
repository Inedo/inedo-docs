---
title: "Create/Update Comment"
order: 1
---

*Create/Update Comment* is available as both a `pgutil` command and an HTTP Request, and will create a new comment or update properties on an existing comment, and return a [CommentInfo Object](/docs/proget/reference-api/proget-api-sca#commentinfo-object) object describing the new or updated comment.

:::(Info) (🚀 Quick Example: Creating a New Comment with Curl)
This example creates a comment in version `1.2.3` of a project named `myProject`, authenticating with the API key `abc12345`:

````
curl -H "X-ApiKey: abc12345" -H "Content-Type: application/json" -X POST -d "{ \"project\": \"myProject\", \"version\": \"1.2.3\", \"comment\": \"This is a new comment.\" }" http://192.168.0.189:8624/api/sca/comments
````
:::

## Command Specification (CLI)
:::(Info) (🚧 Coming Soon 🚧)
The `sca comments create` command is coming soon. Similar to the HTTP Request, it will create a new comment or update properties on an existing comment.
:::

## HTTP Request Specification
To create or update a comment, simply `POST` to the URL with an [appropriate API Key](/docs/proget/reference-api/proget-api-sca#authentication) and a [CommentInfo Object](/docs/proget/reference-api/proget-api-sca#commentinfo-object) object as the request body.

```
POST /api/sca/comments
```

**Creating a comment** with a `CommentInfo` body requires the project `name` (e.g. `myProject`), `version` (e.g. `1.2.3`), and `comment` (e.g. `This is a new comment`)

```json
POST /api/sca/comments

{
    "project": "myProject", 
    "version": "1.2.3", 
    "comment": "This is a new comment"
}
```

**Updating a comment** with a `CommentInfo` body requires the comment `number` (e.g. `1`) project `name` (e.g. `myProject`), `version` (e.g. 1.2.3), and updated `comment` (e.g. `This is a newer comment`)

```json
POST /api/sca/issues

{
  "number":1,
  "project": "myProject", 
  "version": "1.2.3", 
  "comment": "This is a newer comment"
}
```

## HTTP Response Specification

| Response | Details |
| --- | --- |
| **200 (Success)** | indicates the comment was successfully entered |
| **400 (Invalid Input)** | indicates invalid or missing properties on the [CommentInfo](/docs/proget/reference-api/proget-api-sca#commentinfo-object) object; the body will provide some details as text |
|  **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/proget-api-sca#authentication); the body will be empty |
| **500 (Server Error)** | indicates an unexpected error; the body will contain the message and stack trace, and this will also be logged |