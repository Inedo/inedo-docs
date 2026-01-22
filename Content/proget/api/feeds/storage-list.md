---
title: "List Feed Storage Types"
order: 8
---

*List Feed Storage Types* is available as both a `pgutil` command and an HTTP Request, and will return a list describing all feed storage types.

:::(Info) (🚀 Quick Example: Listing feed storage types with pgutil)
This example lists all feed storage types:

```bash
pgutil feeds storage types
```
:::

## Command Specification (CLI)
The `feeds storage types` command is used to list all feed storage types.

:::(info)(📄 Note: pgutil Sources)
Source options must also be specified unless you have the "Default" source configured, and that a feed may be instead specified in the source. See [Working with Sources](/docs/proget/api/pgutil#sources) to learn more.
:::

**Listing all feed storage types** does not require any additional options:

```bash
pgutil feeds storage types
```

Example output:

```plaintext
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
To list all feed storage types, simply `GET` to the URL with an [appropriate API Key](/docs/proget/api/feeds#authentication).

```plaintext
GET /api/storage
```

## HTTP Response Specification
A successful (`200`) response body will contain an array of `FeedStorageType` (see [FeedStorageType.cs](https://github.com/Inedo/pgutil/blob/thousand/Inedo.ProGet/FeedStorageType.cs)) objects. A `500` response indicates a [missing, unknown, or unauthorized API Key](/docs/proget/api/security#authentication).
