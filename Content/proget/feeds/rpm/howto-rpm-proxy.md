---
title: "HOWTO: Aggregate RPM Repositories in ProGet"
order: 1
---

With ProGet teams can create ["Feeds"](/docs/proget/feeds/feed-overview) to aggregate multiple [RPM](https://rpm.org/) repositories from a single source by setting up different ["Connectors"](/docs/proget/feeds/connector-overview). They can then proxy RPM packages from these repositories and install them as they would if accessing a repository directly.

Feeds will also cache RPM packages allowing teams to access them even when repositories they are proxying from are down, and show which packages are being downloaded and used frequently.

In this guide, we'll start by looking at how to create a feed in ProGet and add connectors to proxy packages through one or more RPM repositories. We'll then add this feed to your local RPM environment so that these proxied packages can be accessed and installed.

We'll also look at creating a private repository for when you also want to use internal packages, and how to create a package approval flow if you need to control which packages your team are using in production.

## Step 1: Create a New Feed

First, we will create an RPM feed that will proxy packages from several RPM repositories.

Start by selecting "Feeds" and "Create New Feed". Then select "RPM Packages", which will be listed under the "System & Software Configuration" section.

![](/resources/docs/proget-rpm-createfeed.png){height="" width="50%"}

From here, name your feed. For the example in this guide we will call our feed `centos9-packages`. Then click "Create Feed".

![](/resources/docs/proget-rpm-feedname-centos.png){height="" width="50%"}

You'll then be redirected us to your RPM feed, which will appear empty for now.

![](/resources/docs/proget-rpm-feed-centos-empty.png){height="" width="50%"}

## Step 2: Create a Connector

Now we'll add connectors to our `centos9-packages` feed to aggregate several RPM repositories. To add a connector, navigate to "Feeds" > "Connectors" and select "Create Connector".

![](/resources/docs/proget-addconnector.png){height="" width="50%"}

Then select "Other Connectors" and find "RPM Connector" in the list.

![](/resources/docs/proget-addconnector-rpm.png){height="" width="50%"}

Give your connector a name, and then enter the URL of the repository in the "Connector URL" field. Add your `centos9-packages` feed in the "Associated Feeds" field and then select "Save".

When creating a connector to an official repository, we recommend using a name that follows the URL conventions. For example, for the repository URL `https://mirror.stream.centos.org/9-stream/BaseOS/x86_64/os/`, we'll call the connector `centos-9-stream-baseos-x86_64-os`

![](/resources/docs/proget-createconnector-rpm-centos.png){height="" width="50%"}

:::(info)(📄 Note: OSS Repository URL)
The exact URL will vary from repository to repository, however you want to enter a URL that links to a directory containing a `repodata` folder which contains the files needed to index the RPM packages within the repository.
:::

Repeat as necessary to create connectors to additional repositories you want to proxy from.

![](/resources/docs/proget-connectors-rpm.png){height="" width="50%"}

Navigating back to your `centos9-packages` feed, it should now be populated with packages proxied from the configured OSS repository.

![](/resources/docs/proget-rpm-feed-packages.png){height="" width="50%"}

## Step 3: Add the Feed to Your Local RPM Environment

For your team to install packages proxied to the `centos9-packages` feed, you'll need to add it as a source in their local environment. For this, you will need feed's URL. This can be found at the top right of the feed's page.

![](/resources/docs/proget-rpm-feed-url.png){height="" width="50%"}

To add the feed, you'll need to create a `.repo` file locally. Create the file by entering:

```bash
$ sudo vi /etc/yum.repos.d/centos9-packages.repo
```

In this case we used the `vi` text editor, but you can use any other such as `nano`. With the `.repo ` file open, enter the following:

```bash
[centos9-packages]
name=centos9-packages
baseurl=http://proget.corp.local/rpm/centos9-packages/ # your RPM feed URL
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

By default, repositories will already be configured, depending on the distribution of your local environment. We recommend removing these to install packages exclusively from your `centos9-packages` feed. You can remove a repository by entering:

```bash
$ sudo rm /etc/yum.repos.d/«repo-name».repo
```

## (Optional) Authenticating to Your RPM Feed

By default your `centos9-packages` feed does not require authentication and can be viewed anonymously. However, you may want to make your feed private and [configure it to require authentication to access](/docs/proget/feeds/rpm#authenticating-to-rpm-yum-feeds). For example, when also hosting your own internal packages.

## (Optional) Creating a Package Approval Flow

This guide covered how to proxy packages from the various RPM public repositories. However, this allows developers to install any OSS packages without oversight. In many cases, it's important to include some form of approval in development or production, which can be done by introducing a ["Package Approval Flow"](/docs/proget/packages/package-promotion).

To set up a package approval flow, refer to [HOWTO: Approve and Promote Open-source Packages](/docs/proget/packages/package-promotion/proget-howto-promote-packages). This guide uses NuGet feeds as an example, but the steps are identical when creating RPM package feeds.

After creating your "Unapproved" and "Approved" feeds, follow the [earlier steps](#step-3-add-the-feed-to-your-local-rpm-environment) to add the "Approved" feed as a source in your local rpm environments, entering:

```bash
baseurl=http://«feed-url»
```

And then confirm that the feed was configured by entering:

```bash
$ yum repolist all
```

Or listing packages in a configured repository named `internal-npm` by entering:

```bash
$ yum list available --disablerepo="*" --enablerepo=internal-npm
```
