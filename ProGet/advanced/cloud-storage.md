---
title: Cloud Storage (Amazon S3 & Azure Blob)
subtitle: Cloud Package Stores
sequence: 100
keywords: proget, storage, cloud, amazon, azure
---

This feature is available in paid and trial ProGet editions. {.info} *[Learn how cloud storage can provide redundancy and infrastructural flexibility - without breaking the bank](https://www.inedo.com/proget/features/cloud-storage/) and [claim a 30-day free trial of ProGet](https://inedo.com/proget/pricing/claim-trial).*

ProGet defaults to storing package files on disk, but you can configure a feed to store packages on the cloud (Amazon S3 or Microsoft Azure Blob) instead. This offers two key benefits:

**Scale when you need** with cloud package stores; this takes the guess work out of your future storage needs. By storing packages on the cloud, you no longer have to worry about maintaining, installing, or provisioning additional storage capacity.

**Disaster recovery** is made simple; both Amazon S3 and Azure Blob storage offer redundancy, meaning that you don't have to worry about complex, disaster recovery plans for large amounts of package files because your files are automatically saved to the cloud.

Cloud storage is generally slower, as it's much faster to read/write files from local disk than it is over an internet connection. However, the speed difference may not be noticed in day-to-day use.

## Configuring a Feed to Use Cloud Storage  {#configure data-title="Configuring a Feed to Use Cloud Storage"}

By default, ProGet stores packages on disk using a disk-based package store.

You can change a feed's package store by going to the Manage Feed page, and clicking *change* next to the Package Store heading. This will open a dialog that allows you to select between Amazon S3 and Microsoft Azure.

:::attention {.technical}
If you don't see Amazon S3 or Microsoft Azure as an option, then validate that those extensions are installed by going to Administration > Extensions
:::

After selecting the package store type, you will be presented with a handful of required configuration options.

### Amazon S3 Options  {#amazon-s3 data-title="Amazon S3 Options"}

|  Option     | Description |
| :------------- | :------------- |
| Access Key & Secret Access Key      | This is the equivalent of a username and password for Amazon Web Services; you can create one with the Amazon IAM console       |
|Bucket Name |	The name of the storage bucket configured on S3 that will be used as a packages store|
|Target Path |	The path within the specified bucket; the default is "/" |
|Reduce Redundancy	| When set, ProGet will request files to be written using a Reduced Redundancy storage class (instead of Standard), which this tends to have a lower storage fee; see S3 documentation to learn more about this |
|Make Public	| When set, the files uploaded will be given public permission to view; this is generally not recommended |
|Encrypted	| When set, ProGet will request server-side encryption is used for packages; this is generally not recommended |
|Region Endpoint |	The region endpoint (such as us-east-1) where the bucket is located|

### Azure Blob Options  {#azure data-title="Azure Blob Options"}

|  Option | Decription      |
| :------------- | :------------- |
| Connection string |	A Microsoft Azure connection string, like  ```DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key```     |
|Container	| The name of the Azure Blob Container that will receive the uploaded files. |
|Target Path |	The path in the specified Azure Blob |

## Migrating an Existing Feed  {#migrate data-title="Migrating an Existing Feed"}

When you change a feed's package store, the package files will not be moved.  To ensure the new package store has the same packages, you can perform the following steps

1. Clear the cached packages
2. Stop the ProGet Service
3. Note the disk path of the feed (&laquo;old-disk-path&raquo;)
4. Navigate to &laquo;old-disk-path&raquo; and "flatten" the directory (see below for example)
5. Change the package store and configure appropriately
6. Set the Drop Path to be &laquo;old-disk-path&raquo; from step 3
7. Start the service
8. Once imported, delete the empty &laquo;old-disk-path&raquo; from step 3

Note that this will only work on feed types that support drop paths. 

### Example PowerShell Script to Flatten a Directory

You can a simple PowerShell command to flatten a directory.

1. In Windows Explorer, navigate to the directory to flatten (i.e. &laquo;old-disk-path&raquo;)
2. File → Open Windows PowerShell
3. Enter the following: `Get-ChildItem -Recurse -File | ForEach-Object {Move-Item $_.FullName .}`

This command will move all files within subfolders to directory the command is run in. Make absolute certain that you run this command in the appopriate directory, because it could do damage somewhere else.
