---
title: "HOWTO: Configure Feeds for Google Cloud"
order: 3
---

ProGet can integrate with Google Cloud Storage to host your package files instead of keeping them on your local server. Using cloud storage helps you scale as your package repository grows, providing more storage and cloud-based disaster recovery options. 

In this guide, we'll guide you through setting up a Google Cloud Storage bucket and configuring ProGet to store packages from a new feed there.

:::(Info) (Migrating an Existing Feed?)
This guide will walk you through creating a new feed. If you have an existing feed that you want to migrate to Google Cloud, read our [migrating an existing feed docs](/docs/proget/cloud-storage#migrating-an-existing-feed).
:::

## Step 1: Login or Create a Google Cloud account.
Sign up or login your [Google Cloud](https://cloud.google.com/).

![](/resources/docs/google-cloud-login.png){height="" width="50%"}

## Step 2: Select or Create a Project
**If you have already have an active project, you can skip this step**.

You will need to select a currently active project, or create one. First select the "Project Picker" at the top left of the Google Cloud console.

![](/resources/docs/google-cloud-project-picker.png){height="" width="50%"}

From here you can either choose an active project or create a new one. 

![](/resources/docs/google-cloud-project-select.png){height="" width="50%"}

## Step 3: Create A Google Cloud Bucket { #create-bucket}

Now you will create the Google Cloud bucket to store your packages. From the navigation bar, navigate to "Cloud Storage" > "Buckets"

![](/resources/docs/google-cloud-buckets-select.png){height="" width="50%"}

Now select "Create" to create a new bucket. You'll be given options to name the bucket, and configure other settings such location and access.

![](/resources/docs/google-cloud-buckets-create.png){height="" width="50%"}

## Step 4: Create a Service Account { #create-account }

Once your bucket is created you'll need to create a Service Account that ProGet will use to access your Google Cloud bucket. From the navigation bar, navigate to "IAM" > "Service Accounts"

![](/resources/docs/google-cloud-serviceaccounts-select.png){height="" width="50%"}

Now, select "create service account".

![](/resources/docs/google-cloud-serviceaccounts-create.png){height="" width="50%"}

From here you will create a name for the service account, and define the permissions. It's important that you select a role with the correct permissions that allow ProGet to access the Google Cloud bucket. 

![](/resources/docs/google-cloud-serviceaccounts-setup.png){height="" width="50%"}

## Step 5: Create a Service Account Key { #create-key }

Now you've created the Service Account, you will need to generate a key that you will enter when configuring your cloud storage in ProGet. Select the service account you just created from the Service Account page of the dashboard, navigate to the "Keys" tab and select "Add key" > "Create new key".

![](/resources/docs/google-cloud-serviceaccounts-createkey.png){height="" width="50%"}

Select "Json", which will create and download a Json file locally.

![](/resources/docs/google-cloud-serviceaccounts-json.png){height="" width="50%"}

## Step 6: Create a New Feed 
To create a new feed, navigate to Feeds and select Create new feed.

![Create New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}
    
Next, you need to select the type of feed you want to set up. In this tutorial we will set up a NuGet feed, but the steps should be nearly identical for each feed. Select the feed you want to set up and fill in the appropriate fields.

![Create NuGet Feed](/resources/docs/proget-nuget-newfeed.png){height="" width="50%"}
    
## Step 7: Change Your Feed’s Package Store Location
ProGet stores package files on disk by default, so you will need to configure your feed to store packages on Google Cloud instead. In your feed, click Manage Feed > Storage & Retention, and then select Change next to Storage.

![Change Storage Location](/resources/docs/proget-nuget-changestorage.png){height="" width="50%"}
    
After clicking Change, you will be prompted to select a storage location. Select "Google Cloud".

![](/resources/docs/proget-cloudstorage-select-google.png){height="" width="50%"}

## Step 8: Configure Package Store Location to Use Your Google Cloud Bucket

Next, you will be presented with the following configuration options:

![](/resources/docs/proget-cloudstorage-configure.png){height="" width="50%"}

Configure the options as follows:

* **Bucket**: Enter the name of the bucket you created in [Step 3](#create-bucket). We called our's `proget-internal-nuget` here.
* **Prefix**: Enter the path within the specified bucket, or leave it blank to use the bucket root
* **Service account key**: Open the `.json` file you downloaded in [Step 5](#create-key) and copy/paste the contents into here.

Once configured you'll be given the option to test your configuration. You can also do this any time by selecting "test" to the right of "Storage" under "Storage Configuration" on the feed's "Manage Feed" page. For more details on all of the configuration options, you can read our [Cloud Storage Overview](/docs/proget/cloud-storage).

![](/resources/docs/proget-cloudstorage-google-test.png){height="" width="50%"}

## (Optional) Setting up ProGet as a UI for Google Cloud

In addition to configuring your feed to store packages in Google Cloud, you can use ProGet as a UI for managing other assets you may be using it to store. This gives users a simple way to browse, upload, and organize files without navigating Google Cloud’s console, while administrators maintain full control over access and permissions. To learn more, see [HowTo: Set Up ProGet as a UI for Cloud Storage](https://blog.inedo.com/proget/proget-as-cloud-storage-ui).

## Troubleshooting:

### Issue: Google Cloud is not a storage option
If you can't see the Google Cloud (or any) storage option, the Google Cloud extension is probably not installed. This should be installed by default, but to confirm, navigate to "Administration" > "Extensions". Then, check if it appears in the list under "Installed Extensions".

If Google Cloud isn't installed, it should appears as an option under "Available Extensions". Selecting this will let you install Google Cloud to ProGet.

### Error: The package could not be installed
If you receive the error message "The package could not be installed." when adding/pushing a package, or if the connection test fails, it usually means that ProGet received an error while connecting to the Google Cloud bucket. Please check the following:

* Verify the bucket name is correct, and correctly formatted. 
* Verify the Service Account key `.json` is correct, and that the key has the correct permissions set (as covered in [Step 4](#create-key))