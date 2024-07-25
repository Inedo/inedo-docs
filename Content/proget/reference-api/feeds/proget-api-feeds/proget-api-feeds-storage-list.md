---
title: "List Feed Storage Types"
order: 8
---

*List Feed Storage Types* is available as both a `pgutil` command and an HTTP Request, and will return an array of [FeedStorageType](/docs/proget/reference-api/feeds/proget-api-feeds#type-object) objects, describing all feed storage types.

:::(Info) (🚀 Quick Example: Listing feed storage types with pgutil)
This example lists all feed storage types

```
pgutil feeds storage types
```
:::

## Command Specification (CLI)
The `feeds storage types` command is used to list all feed storage types.

**Listing all feed storage types** does not require any additional options:

```
pgutil feeds list
```

Example output:

```
Type: disk
Storage path on the local file system or network share.
Options:
  --StoragePath=<value>
    Local file system path or network share.
    Default: managed by ProGet

Type: s3
Stores packages and assets in an Amazon S3 bucket.
Options:
  --AccessKey=<value>
  --SecretAccessKey=<value>
  --RegionEndpoint=<value> *REQUIRED*
  --BucketName=<value>
  --TargetPath=<value>
    Default: none (use bucket root)
  --MakePublic=<value>
    Must be true or false (defaults to false).
  --Encrypted=<value>
    Must be true or false (defaults to false).
  --InstanceRole=<value>
    This overrides the access key and secret key; only available on EC2 instances or ECS Tasks.
  --UseInstanceProfile=<value>
    When using an IAM Role, this indicates if the role is an instance profile.
    Must be true or false (defaults to false).
  --CustomServiceUrl=<value>
    Specifying a custom service URL will override the region endpoint.

Type: azure
A file system backed by Microsoft Azure Blob Storage.
Options:
  --ConnectionString=<value> *REQUIRED*
    A Microsoft Azure connection string, like
    <code>DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key</code>
  --ContainerName=<value> *REQUIRED*
    The name of the Azure Blob Container that will receive the uploaded files.
  --TargetPath=<value>
    The path in the specified Azure Blob Container that will received the uploaded files; the default is the root.
```

## HTTP Request Specification
To list all feed storage types, simply `GET` to the URL with an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication).

```
GET /api/storage
```

## HTTP Response Specification
A successful (`200`) response body will contain an array of [FeedStorageType](/docs/proget/reference-api/feeds/proget-api-feeds#type-object) objects. For example:

```
GET /api/storage

[
    {
    "id": "azure",
    "name": "Microsoft Azure",
    "description": "A file system backed by Microsoft Azure Blob Storage.",
    "properties": {
        "ConnectionString": {
        "required": true,
        "name": "Connection string",
        "description": "A Microsoft Azure connection string"
        },
        "ContainerName": {
        "required": true,
        "name": "Container",
        "description": "The name of the Azure Blob Container"
        },
        "TargetPath": {
        "name": "Target path",
        "description": "The path in the specified Azure Blob Container"
        }
      }
    },
    { ... } // remaining feed storage options
]
```

| Response | Details |
|---|---|
| **200 (Success)** | body will contain an array of [FeedStorageType](/docs/proget/reference-api/feeds/proget-api-feeds#type-object) objects |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication); the body will be empty |