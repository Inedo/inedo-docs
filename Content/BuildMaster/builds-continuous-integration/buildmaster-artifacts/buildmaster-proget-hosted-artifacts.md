---
title: "HOWTO: Store BuildMaster Artifacts in ProGet"
order: 2
---

As of BuildMaster v7.0.17, artifacts can be hosted in a [ProGet Asset Directory](/docs/proget/asset-directories-file-storage/what-is-an-asset-directory). 
* Allow for artifacts to be directly downloaded outside of BuildMaster
* Use [ProGet's Cloud Storage Feature](/docs/proget/cloud-storage) to store artifacts on Amazon S3 or Azure Blob
* Simplify [replication/distribution to nodes](/docs/proget/replication-feed-mirroring/proget-howto-replicate-edge-locations) in edge-computing scenarios

## How to Use ProGet-hosted Artifacts
This can be done globally and at the application level.

1. Create an Asset Directory in ProGet
2. Add ProGet Connection in BuildMaster (Admin > ProGet Integration) if not already added
   a. Verify Asset Directory shows up and is not read-only
5. Navigate to Administration > Storage > Artifacts and click Change Settings
6. Select ProGet Asset Directory and then the asset directory

You can configure artifact storage options at the application level, under the Application Settings tab.

Note that you can [migrate existing artifact files](/docs/proget/asset-directories-file-storage/proget-howto-migrating-from-a-network-share-to-hosted-files) if needed; the path can be found under Administration > Storage & Backup > Artifacts.