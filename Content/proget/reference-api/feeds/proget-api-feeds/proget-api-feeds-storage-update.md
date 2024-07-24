---
title: "Update Feed Storage"
order: 7
---

*Update Feed Storage* is available as both a `pgutil` command and an HTTP Request, and will update a specified feed's storage configuration using the [FeedStorageConfiguration](/docs/proget/reference-api/feeds/proget-api-feeds#storage-object) object properties defined in the request body.

:::(Info) (🚀 Quick Example: Updating a feed's storage configuration with pgutil)
This example updates the storage of the feed `myNugetFeed` to `azure`
```
pgutil feeds storage change --feed=myNugetFeed --type=azure --ConnectionString=DefaultEndpointsProtocol=https;AccountName=myazurestorage;AccountKey=H+2dPzlkXN3k5r8GwS1o9YX3u5pAzU+8LWosFSQxBTG1CRl3q8k9ZjQz1qE1ZnxyG+0jl5vKlRjN2o2MWwzA==;EndpointSuffix=core.windows.net --ContainerName=projectdocuments --TargetPath=projectdocuments/uploads/2024/07/
```
:::

## Command Specification (CLI)
The `feeds storage change` command is used to update a feed's storage configuration.

The `--feed` and `--type` options are always required. Additional options are required, depending on the storage type.

**Updating a feed's storage type to `disk`** requires the feed name (e.g. `myNugetFeed`), the storage type (e.g. `disk`) and the storage path (e.g. `C:\ProgramData\Proget\Packages`):
```
pgutil feeds storage change --feed=myNugetFeed --type=disk --StoragePath=C:\ProgramData\Proget\Packages
```

**Updating a feed's storage type to `s3`** requires the feed name (e.g. `myPypiFeed`), the storage type (e.g. `disk`), and the region endpoint (e.g. `us-east-1`). The following options can also be included:

| Option | Description | Example
| --- | --- | --- |
| --AccessKey | User's access key | `AKIAIOSFODNN7EXAMPLE` |
| --SecretAccessKey | User's secret access key | `wJalrXUtnFEMI/K7MDENG/bPxRfiCYKEY` |
| --BucketName | Name of the S3 bucket | `my-bucket` |
| --TargetPath | (Default: `none`) | `backups/2024/July`|
| --MakePublic | Setting to `true` will make data publicly available. (default `false`) | `false` |
| --Encrypted | Setting to `true` will encrypt the data. (default `false`) | `false` |
| --InstanceRole | Overrides access key and secret key; only available on EC2 instances or ECS Tasks. | `arn:aws:iam::123456789012:instance-profile/S3AccessRole` |
| --UseInstanceProfile | When using an IAM Role, indicates if role is an instance profile. (default `false`). | `false` |
| --CustomServiceUrl | Specifying a custom service URL will override the region endpoint. | `https://proget.corp.local` |

```
pgutil feeds storage change --feed=myPypiFeed --type=s3 --RegionEndpoint=us-east-1 --AccessKey=AKIAIOSFODNN7EXAMPLE --SecretAccessKey=wJalrXUtnFEMI/K7MDENG/bPxRfiCYKEY
```

**Updating a feed's storage type to `azure`** requires the feed name (e.g. `myNpmFeed`), the storage type (e.g. `disk`), the connection string (e.g. `DefaultEndpointsProtocol=https;AccountName=myazurestorage;AccountKey=H+2dPzlkXN3k5r8GwS1o9YX3u5pAzU+8LWosFSQxBTG1CRl3q8k9ZjQz1qE1ZnxyG+0jl5vKlRjN2o2MWwzA==;EndpointSuffix=core.windows.net`), and the container name (e.g. projectdocuments). Optionally, a target path can be included (e.g. `projectdocuments/uploads/2024/07/`)

```
pgutil feeds storage change --feed=myNpmFeed --type=azure --ConnectionString=DefaultEndpointsProtocol=https;AccountName=myazurestorage;AccountKey=H+2dPzlkXN3k5r8GwS1o9YX3u5pAzU+8LWosFSQxBTG1CRl3q8k9ZjQz1qE1ZnxyG+0jl5vKlRjN2o2MWwzA==;EndpointSuffix=core.windows.net --ContainerName=projectdocuments --TargetPath=projectdocuments/uploads/2024/07/
```

## HTTP Request Specification
To update a feed, simply `POST` to the URL with the `feed` name, an [appropriate API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication) and a [FeedStorageConfiguration](/docs/proget/reference-api/feeds/proget-api-feeds#storage-object) object as the request body.

:::(internal)
Need to test what happens when you post with incomplete values, then write about it
:::

```
POST /api/storage?feed=«feed-name»
```

## HTTP Response Specification
A successful (`200`) response body will contain an updated [FeedStorageConfiguration](/docs/proget/reference-api/feeds/proget-api-feeds#storage-object) object. For example, when updating the storage configuration of a NuGet feed `myNugetFeed`, this returns:

```
POST /api/storage?feed=myNugetFeed

{

}
```

| Response | Details |
|---|---|
| **200 (Success)** | response body contains an updated [FeedStorageConfiguration](/docs/proget/reference-api/feeds/proget-api-feeds#storage-object) object|
| **400 (Invalid Input)** | indicates invalid or missing properties in the request; the body will provide some details as text |
| **403 (Unauthorized API Key)** | indicates a [missing, unknown, or unauthorized API Key](/docs/proget/reference-api/feeds/proget-api-feeds#authentication); the body will be empty |
| **404 (Feed Not Found)** | indicates that the specified `feed` does not exist |