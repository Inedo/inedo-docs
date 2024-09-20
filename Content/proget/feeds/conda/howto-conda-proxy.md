---
title: "HOWTO: Proxy OSS Conda Packages from Anaconda Packages"
order: 1
---

With ProGet teams can proxy packages from the [Anaconda OSS Package Repository](https://repo.anaconda.com/) and consume them in their projects as they would if they were pulling from the OSS package repository directly. 

The advantages of using ProGet for this are:
* ProGet will cache packages allowing teams to access them even when the Anaconda Packages repository is down
* ProGet will show which packages are being downloaded and used frequently

In this article, we'll explain how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet that will proxy Conda packages from the public OSS Repository. 

We'll also look at creating a private repository for when you also want to use internal packages, and how to create a package approval flow if you need to control which packages your team are using in production. 

## Step 1: Create a New Feed { #step-1 }

First, we will create a Conda feed that will proxy packages from [Anaconda OSS Package Repository](https://repo.anaconda.com/).

Start by selecting "Feeds" and "Create New Feed".

![New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Next, select "Conda Packages", as we will be creating feeds to proxy and host Conda packages.

![Select Conda](/resources/docs/proget-conda-newfeed.png){height="" width="50%"}

Now select "Connect to Anaconda Packages" which will allow us to proxy feeds from the [Anaconda OSS Package Repository](https://repo.anaconda.com/)

![Connector](/resources/docs/proget-conda-newfeed-connector.png){height="" width="50%"}

Then select "No, Create One Feed", as we will be creating a single feed to proxy Conda packages.

![One Feeds](/resources/docs/proget-conda-onefeed.png){height="" width="50%"}

## Step 2: Name Your Feed

From here, we name our feed, which in this example we will call `public-conda`. Then click "Create Feed".

![Name Feed](/resources/docs/proget-conda-onefeed-name.png){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) documentation.

![SCA Features](/resources/docs/proget-conda-newfeed-sca.png){height="" width="50%"}

Finally, we select [Set Feed Features], which will create the feeds, and redirect us to our `public-conda` feed, now populated with packages proxied from the public OSS Conda repository.

![Feed](/resources/docs/proget-conda-publicfeed.png){height="" width="50%"}

## Step 3: Adding the Feed to Local Conda Environments { #step-3 }

For your team to consume packages from the `public-conda` feed, you'll need to add it as a channel in their local environment. For this, you will need feed's URL. This can be found at the top right of the feed's page.

![Feed](/resources/docs/proget-conda-publicfeed-url.png){height="" width="50%"}

In your terminal of choice, enter the following, which will require the `public-conda` feed URL:

```bash
$ conda config --add channels «feed-url»
```

For example, adding a feed with the URL `http://proget.corp.local/conda/public-conda/` you would enter:

```bash
$ conda config --add channels http://proget.corp.local/conda/public-conda
```

You can confirm that it was registered by entering:

```bash
$ conda config --show channels
```

Finally, to ensure that developers consume packages from the `public-conda` feed rather than the OSS repository, we recommend removing the `defaults` channel, which exists by default. This can be done by entering:

```bash
$ conda config --remove channels defaults
```

## Step 4: (Optional) Confirming Connection to your Conda Feed

You can confirm that your local Conda environment can connect with your ProGet feed by listing Conda packages from the feed by entering:

```bash
$ conda search -c «feed-url»
```

Or by filtering by package name:

```bash
$ conda search -c «feed-url» «package-name»
```

## (Optional) Authenticating to Your Conda Feed

By default your `public-conda` feed will not require authentication and can be viewed anonymously. However you may want to make your repository private and require authentication to access it. This is recommended when hosting your own internal packages in a feed, in addition to proxied packages from the OSS repository. 

First navigate to navigate to "Settings"> "Manage Security", and remove anonymous access by clicking the small "X" in the "Anonymous" entry. 

![Permissions Remove](/resources/docs/proget-conda-permissions-remove.png){height="" width="50%"}

Now you will need to create an [API Key](/docs/proget/reference-api/proget-apikeys). 

Start by navigating to "Administration Overview" > "API Keys & Access Logs" under "Security & Authentication"

![Admin Overview](/resources/docs/proget-admin-apikeys.png){height="" width="50%"}

Then select "Create API Key"

![Create Key](/resources/docs/proget-apikey-new.png){height="" width="50%"}

Then fill in the fields by selecting "Feeds ("Use Certain Feeds)" as the "Feed Type" and selecting the `public-conda` feed. Then set the API key. You can specify any alphanumeric sequence for this, or leave it blank to autogenerate one.

Ensure that the "View/Download" box is checked, and then select "Save".

![API Key](/resources/docs/proget-conda-apikey-3.png){height="" width="50%"}

Now, we'll add the feed to a local Conda environment. Instead of adding the URL like in [Step 3](#step-3), enter the following, containing both URL and API Key:

```bash
$ conda config --add channels http://api:«api-key»@«feed-url»
```

For example, when authenticating with the API key abc12345 to the URL `http://proget.corp.local/conda/public-conda/` you would enter:

```bash
$ conda config --add channels http://api:abc12345@192.168.0.129:8624/conda/public-conda/
```

Confirm that it was registered by entering:

```bash
$ conda config --show channels
```

## (Optional) Creating a Package Approval Flow

So far we've looked at proxying packages from an OSS repository. However you may want to make sure that only approved packages are used in your development or production environment to avoid risks related to quality, vulnerabilities, licenses, etc.

In ProGet, you can create a "Package Approval Flow" that involves promoting packages between feeds to ensure that only approved and verified packages are used in the right environments, such as production. 

You can read more about [package promotion in our documentation](/docs/proget/packages/package-promotion), however here we will explain how to set up package promotion by creating feeds and configuring permissions. 

### Step 1: Create New Feeds

We will create two Conda feeds for our package approval flow. One feed that we will call `unapproved-conda` will proxy packages from the [Anaconda OSS Package Repository](https://repo.anaconda.com/), much like the `public-conda` feed we created earlier. A second feed that we will call `approved-conda` will host packages that have been promoted and approved for use in production. 

Start by following the same steps found in [Step 1](#step-1), however when given the choice of creating one or two feeds, select "Yes, Create Two Feeds", as we will be creating our `unapproved-conda` and `approved-conda` feeds.

![Two Feeds](/resources/docs/proget-conda-twofeeds.png){height="" width="50%"}

From here, we name our feed, which in this example we will call `public-conda`, and then click "Create Feeds".

![Name Feed](/resources/docs/proget-conda-twofeeds-name.png){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) documentation.

![SCA Features](/resources/docs/proget-conda-newfeeds-sca.png){height="" width="50%"}

Finally, we select [Set Feed Features], which will create the feeds, and redirect us to our `unapproved-conda` feed, now populated with packages proxied from the public OSS Conda repository.

![Feed](/resources/docs/proget-conda-unapprovedfeed.png){height="" width="50%"}

## Step 2: Set Permissions

Now we need to [configure security access controls for uses and groups](/docs/proget/administration-security) to set permissions that allow only certain team members to promote packages to the `approved-conda` feed. In this example we will assign our Lead Developers this permission, since they're trained to check the quality, licenses, and vulnerabilities of open-source packages. By default, only administrators have assigned permissions.

To start, navigate to "Settings"> "Manage Security".

![Manage Security](/resources/docs/proget-settings-managesecurity.png){height="" width="50%"}

Then navigate to the "Tasks / Permissions" tab, listing the currently configured permissions.

If you haven't already, remove anonymous access by clicking the small "X" in the "Anonymous" entry. This will prevent developers accessing any feed other than the `approved-conda` feed that we will specify in a later step.

![Permissions Remove](/resources/docs/proget-conda-permissions-remove.png){height="" width="50%"}

Next, select "add permission"

![Add Permission](/resources/docs/proget-taskspermissions-add.png){height="" width="50%"}

Now fill out the following dialog to give the "Lead Developers" user group permission to "View/Download" and "Promote Packages" from the `unapproved-conda` feed.

![Permit Leads](/resources/docs/proget-conda-addprivilages-leaddevelopers.png){height="" width="50%"}

Next add another permission, this time allowing anonymous access to the `approved-conda` feed to "View/Download" packages.

![Permit Leads](/resources/docs/proget-conda-anonpermissions.png){height="" width="50%"}

After saving these permissions, the task overview page looks like this:

![Permissions Overview](/resources/docs/proget-conda-permissions.png){height="" width="50%"}

## Step 3: Adding the Feed to Local Conda Environments

Finally, follow the steps in [Step 3](#step-3) to add the `approved-conda` feed as a channel to your local Conda environments, entering:

```bash
$ conda config --add channels «feed-url»
```

Finally, to ensure that developers only consume packages from the `approved-conda` feed rather than the OSS repository, we recommend removing the `defaults` channel, which exists by default. This can be done by entering:

```bash
$ conda config --remove channels defaults
```





