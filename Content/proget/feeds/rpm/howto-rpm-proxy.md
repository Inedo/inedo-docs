---
title: "HOWTO: Proxy RPM Packages from OSS Repositories"
order: 1
---

With ProGet teams can proxy packages from [RPM](https://rpm.org/) OSS repositories such as those listed on [rpmfind](https://rpmfind.net) to install and use them in their projects as they would if they were pulling from the repository directly. 

The advantages of using ProGet for this are:
* ProGet will cache packages allowing teams to access them even when the repository they are proxying from is down
* Use multiple [Connectors](/docs/proget/feeds/connector-overview), allowing you to aggregate multiple repositories
* ProGet will show which packages are being downloaded and used frequently

In this guide, we'll start by looking at how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet and add connectors to proxy packages through one or more OSS repositories. We'll then add this feed to your local RPM environment so that proxied packages can be installed. 

We'll also look at creating a private repository for when you also want to use internal packages, and how to create a package approval flow if you need to control which packages your team are using in production. 

## Step 1: Create a New Feed { #step-1 }

First, we will create an RPM feed that will proxy packages from one or more RPM OSS repositories. 

Start by selecting "Feeds" and "Create New Feed". Then select "RPM Packages", which will be listed in the "System & Software Configuration" section.

![](){height="" width="50%"}

From here, mame your feed. For the example in this guide we will call our feed `public-rpm`. Then click "Create Feed".

![](){height="" width="50%"}

You'll then be redirected us to your RPM feed, which will appear empty for now.

![](){height="" width="50%"}

## Step 2: Create a Connector { #step-2 }

Now we'll add a connector to our `public-rpm` feed to proxy packages from an RPM OSS repository. Unlike other package types there are dozens of public repositories for RPM, many of which can be found on [rpmfind](https://rpmfind.net). 

To add a connector, navigate to "Feeds" > "Connectors" and select "Create Connector".

![](){height="" width="50%"}

Then select "Other Connectors" and find "RPM Connector" in the list.

![](){height="" width="50%"}

Give your connector a name, and then enter the URL of the OSS repository in the "Connector URL" field. Add your `public-rpm` feed in the "Associated Feeds" field and then select "Save". 

In this example we will be using the URL `https://mirror.stream.centos.org/9-stream/BaseOS/x86_64/os/`, the base repository for `CentOS Stream 9`.  

![](){height="" width="50%"}

:::(info)(📄 Note: OSS Repository URL)
The exact URL will vary from repository to repository, however you want to enter a URL that links to a directory containing a `repodata` folder which contains the files needed to index the RPM packages within the repository. 
:::

Navigating back to your `public-rpm` feed, it should now be populated with packages proxied from the configured OSS repository.

![](){height="" width="50%"}

Repeat as necessary to create connectors to any additional repositories you want to proxy from. 

## Step 3: Add the Feed to Your Local RPM Environment { #step-3 }

For your team to install packages from the `public-rpm` feed, you'll need to add it as a channel in their local environment. For this, you will need feed's URL. This can be found at the top right of the feed's page.

![](){height="" width="50%"}

To add the feed, you'll need to create a `.repo` file locally. Create the file by entering: 

```bash
$ sudo vi /etc/yum.repos.d/public-rpm.repo  
```

In this case we used the `vi` text editor, but you can use any other such as `nano`. With the `.repo ` file open, enter the following:

```bash
[public-rpm]
name=PublicRpm 
baseurl=http://proget.corp.local/rpm/public-rpm/ # your RPM feed URL
enabled=1 
gpgcheck=0 
```

Then save and exit (`:wq` in the case of `vi`). You can confirm the feed has been set by listing the configured repositories:

```bash
$ yum repolist all
```

Or listing packages by entering:

```bash
$ yum list available --disablerepo="*" --enablerepo=public-rpm
```

By default, repositories will already be configured, depending on the distribution of your local environment. We recommend removing these to install packages exclusively from your `public-rpm` feed. You can remove a repository by entering:

```bash
$ sudo rm /etc/yum.repos.d/«repo-name».repo
```

## (Optional) Authenticating to Your RPM Feed

By default your `public-rpm` feed does not require authentication and can be viewed anonymously. However, you may want to make your feed private and configure it to require authentication to access. For example, when hosting your own internal packages, either solely or in addition to using OSS packages from proxied RPM repositories. 

You can read more about creating API keys in ProGet on our [API Key](/docs/proget/reference-api/proget-apikeys) page. 

When creating an API Key you will need to fill in the fields by selecting "Feeds ("Use Certain Feeds)" as the "Feed Type" and selecting the `public-rpm` feed, and make sure that the "View/Download" box is checked, and then select "Save".

![](){height="" width="50%"}

Alternatively you can create a "Personal API Key", which lets users create/delete API keys that are tied to their username.

Now, we'll add the feed to a local RPM environment which will require the URL from [Step 3](#step-3), as well as your API key. When editing the `.repo` file, enter your API key and URL in the baseurl parameter:

```bash
baseurl=http://api:«api-key»@«feed-url»
```

For example when authenticating with the API key abc12345 to the public-rpm feed, your `.repo` file should look like this:

```bash
[proget]
name=ProGet 
baseurl=http://api:abc12345@proget.corp.local/rpm/public-rpm/  
enabled=1 
gpgcheck=0 
```

## (Optional) Creating a Package Approval Flow

This guide covered how to proxy packages from the various RPM public repositories. However, this allows developers to install any OSS packages without oversight. In many cases, it's important to include some form of approval in development or production, which can be done by introducing a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). This guide uses NuGet feeds as an example, but the steps are identical when creating RPM package feeds.

After creating your "Unapproved" and "Approved" feeds, follow the steps in [Step 3](#step-3) to add the "Approved" feed as a source in your local Ruby environments, entering:

```bash
baseurl=http://«feed-url»
```

And then confirm that the feed was configured by entering:

```bash
$ yum repolist all
```

Or listing packages in a configured repo named `internal-rpm` by entering:

```bash
$ yum list available --disablerepo="*" --enablerepo=internal-rpm
```
