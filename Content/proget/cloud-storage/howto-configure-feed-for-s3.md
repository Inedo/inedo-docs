---
title: "HOWTO: Configure Feeds for Amazon S3"
order: 2
---


ProGet can use Amazon's S3 Cloud to store your package files instead of using your server's drive. This not only ensures that you never run out of storage space, but also simplifies disaster recovery: everything is already stored in the cloud.

In this article, you will learn how to create an S3 bucket and then use it to store packages in a new feed in ProGet.

:::(Info) (Migrating an Existing Feed?)
This guide will walk you through creating a new feed. If you have an existing feed that you want to migrate to Amazon S3, read our [migrating an existing feed docs](/docs/proget/cloud-storage#migrating-an-existing-feed).
:::

## Step 1: Login or Create an Amazon AWS Account.
Sign up or login your [Amazon AWS account](https://aws.amazon.com/).

![Amazon S3 Login](/resources/docs/amazons3-signin.png){height="" width="50%"}

## Step 2: Create an S3 Bucket
**If you have already set up an S3 Bucket, you can skip this step**.

Navigate to the Buckets tab within Amazon S3. If you have trouble finding it, search "Bucket" in the search bar above.
	
![Amazon S3 Buckets Tab](/resources/docs/amazons3-bucketstab.png){height="" width="50%"}

Click on "Create Bucket" and fill in the appropriate fields. When you are done, you should see the name of your bucket, region, and access. In this example, we named our bucket "kramerica" and set the region to US East (Ohio) us-east-2.

![Amazon S3 Buckets Info](/resources/docs/amazons3-bucketsinfo.png){height="" width="50%"}

## Step 3: Create Access Key/ Secret Access Key
After you have set up your bucket, you will need to create an access and secret access key that will be used in ProGet later. Navigate to the Identity and Access Management (IAM) in AWS or simply [click here](https://console.aws.amazon.com/iam/home?#/security_credentials) to go straight to your access keys. 

![Amazon S3 Access key](/resources/docs/amazons3-createaccesskey.png){height="" width="50%"}

Click the blue "Create new access key" button and select the blue arrow that says "View access key" to view your access key and secret access key.

![Amazon S3 Secret Access Key](/resources/docs/amazons3-secretaccesskey.png){height="" width="50%"}

Copy or write down both your access key and your secret access key, as you will need to enter them into ProGet in step 5.

## Step 4: Create a New Feed 
To create a new feed, navigate to the banner at the top of the page and click Feeds. Then select Create new feed.

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}
    
Next, you need to select the type of feed you want to set up. In this tutorial we will set up a NuGet feed, but the steps should be nearly identical for each feed. Select the feed you want to set up and fill in the appropriate fields.

![Create NuGet Feed](/resources/docs/proget-nuget-newfeed.png){height="" width="50%"}

## Step 5: Change Your Feed’s Package Store Location
By default, ProGet stores package files on disk, so your feed must be configured to store packages on Amazon S3 instead. In your feed, click Storage & Retention, and then select Change next to Storage.

![Change Storage Location](/resources/docs/proget-nuget-changestorage.png){height="" width="50%"}
    
After clicking Change, you will be prompted to configure your feed package store. Select "Amazon S3".

![](/resources/docs/proget-cloudstorage-select-s3.png){height="" width="50%"}

## Step 6: Configure Package Store Location to Use Your Amazon S3 Bucket
After you select Amazon S3 as the storage location for your package, you will be presented with the following configuration options:

![Amazon S3 Configuration](/resources/docs/proget-feeds-configureamazons3.png){height="" width="50%"}

Since we are setting up a NuGet feed to use an Amazon S3 bucket, we are configuring only the options required for it.

* **Access Key & Secret Access Key**: Enter the access key and secret access key you created in step 3.
* **Region Endpoint**: Enter the region endpoint you set up in step 2. In our example, the region endpoint is us-east-2.
* **Bucket**: Enter the name of the bucket you created. In our example, we named the bucket kramerica.

Once configured you'll be given the option to test your configuration. You can also do this any time by selecting "test" to the right of "Storage" under "Storage Configuration" on the feed's "Manage Feed" page. 

![](/resources/docs/proget-cloudstorage-s3-test.png){height="" width="50%"}

Thats all you need to configure a new feed to use an Amazon S3 bucket! For details on all of the configuration options, check out our docs on [Cloud Package Stores](/docs/proget/cloud-storage).

:::(Info) (Can't find the needed information to fill out a tab?)
If you are having trouble finding something, a quick Google search will usually suffice, but in case you can not find something, here are some references to commonly used terms:

* Access Key: Your access keys can be found [here](https://console.aws.amazon.com/iam/home?#/security_credentials). 
* Region Endpoint: Using the navigation pane on the left side of your screen in Amazon S3, go to Buckets > Bucket Overview > AWS Region. 
:::

## (Optional) Setting up ProGet as a UI for Amazon S3

Once your feed is connected to Amazon S3, consider using ProGet as UI for managing any other assets you're using it to store. This makes it easier for users to handle uploads and file organization without relying on the S3 console, while administrators retain oversight of access control and permissions. To learn more, see [HowTo: Set Up ProGet as a UI for Cloud Storage](https://blog.inedo.com/proget/proget-as-cloud-storage-ui).

## Troubleshooting:

### Issue: Amazon S3 is not a storage option
If you have reached step 5 and only notice that there is no option to select Amazon S3, the Amazon AWS extension is probably not installed. This should be installed by default, but to confirm, you can navigate to "Administration" > "Extensions". Check if "Amazon AWS" appears in the list under "Installed Extensions".

If it isn't installed, it should appears as an option under "Available Extensions". Selecting this will let you install Amazon AWS to ProGet.

### Error: The package could not be installed
If you receive the error message "The package could not be installed." when adding/pushing a package, it usually means that ProGet received an error while connecting to the Amazon S3 bucket. Please check the following:

- Verify the Access Key and Secret Access Key are properly set from the [Identity and Access Management (IAM) console](https://console.aws.amazon.com/iam/home?#/security_credentials)
- Verify the Region Endpoint is correct. (Ex: if your bucket says `US East (Ohio) us-east-2`, the value should be `us-east-2`)
- Verify _Bucket_ is set to the same name as your S3 bucket (In our example, we named our Bucket `kramerica`)

