---
title: "HOWTO: Proxy OSS Conda Packages from Anaconda Packages"
order: 1
---

With ProGet teams can proxy packages from the [Anaconda OSS Package Repository](repo.anaconda.com) and control access to ensure that only approved packages are used within their environment.

By default, Conda pulls and publishes packages from the `defaults` channel. This poses the risk of developers using packages that may have security or quality issues, or makes it challenging for teams working in offline environments. 

In this article, we'll explain how to proxy Conda packages from the public OSS Repository and create a package approval flow that makes sure developers are only using pre-approved packages in their development. 

## Step 1: Create New Feeds

First, we will create to Conda feeds, one for unapproved packages, and the other for approved packages that will contain packages that have been approved for development.

Start by selecting "Feeds" and "Create New Feed".

![New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Next, select "Conda Packages", as we will be creating feeds to proxy and host Conda packages.

![Select Conda](){height="" width="50%"}

Now select "Connect to Anaconda Packages" which will allow us to proxy feeds from the [Anaconda OSS Package Repository](repo.anaconda.com)

![Connector](){height="" width="50%"}

Then select "Yes, Create Two Feeds", as we will be creating two feeds; `unapproved-conda` to proxy OSS Conda packages and `approved-conda` to host approved packages.

![Two Feeds](){height="" width="50%"}

From here, we name our feeds as specified above, and then click "Create Feeds".

![Name Feed](){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) documentation.

![SCA Featuers](){height="" width="50%"}

Finally, we select [Set Feed Features], which will create the feeds, and redirect us to our `unapproved-conda` feed, now populated with packages proxied from the public OSS Conda repository.

![Feed](){height="" width="50%"}

## Step 2: Set Permissions

Now we need to [configure security access controls for uses and groups](/docs/proget/administration-security) to permit only network engineers to promote packages to the `approved-conda` feed, since they're trained to check the quality, licenses, and vulnerabilities of open-source packages. To do this, we'll set up new permissions. By default, only administrators have assigned permissions.

To start, navigate to "Settings"> "Manage Security".

![Manage Security](/resources/docs/proget-settings-managesecurity.png){height="" width="50%"}

Then navigate to the "Tasks / Permissions" tab, listing the currently configured permissions, and select "add permission".

![Add Permission](/resources/docs/proget-taskspermissions-add.png){height="" width="50%"}

Next fill out the following dialog to give the "Network Engineers" user group permission to "Promote Packages" from the `unapproved-conda` feed.

![Permit Engineers](){height="" width="50%"}

Following the same steps, we will also give "Developers" user group permission to "View and Download" packages from the `approved-conda` feed.

![Permit Developers](){height="" width="50%"}

After saving these two privileges, the task overview page looks like this:

![Permissions Overview](){height="" width="50%"}

## Step 3: Create API Key for Authorization

To allow our developers to access the approved-conda feed from their development environment we will need to create an [API Key](/docs/proget/reference-api/proget-apikeys). 

Start by navigating to "Administration Overview" > "API Keys & Access Logs" under "Security & Authentication"

![Admin Overview](){height="" width="50%"}

Then select "Create API Key"

![Create Key](){height="" width="50%"}

Then fill in the fields by selecting "Feeds ("Use Certain Feeds)" as the "Feed Type" and selecting the approved-conda feed. Then set the API key. You can specify any alphanumeric sequence for this, or leave it blank to autogenerate one.

Ensure that the "View/Download" box is checked, and then select "Save" 

![API Key](){height="" width="50%"}

## Step 3: Adding the Feed to Local Conda Environments

Finally we'll add the conda feed to the developers local environment. For this you will need the URL of the `approved-conda` feed. This can be found at the top right of the feed's page.

![Feed](){height="" width="50%"}

In your terminal of choice, enter the following, which will require your API Key and the `approved-conda` feed URL:

```bash
$ conda config --add channels http://api:«api-key»@«feed-url»
```

You can confirm that it was registered by entering:

```bash
$ conda config --show channels
```

Finally, to ensure that developers are only using approved packages from the `approved-conda` feed, we recommend removing the `defaults` channel, which exists by default. This can be done by entering:

```bash
$ conda config --remove channels defaults
```

