---
title: "HOWTO: Publish Conda Packages to a ProGet Feed"
order: 2
---

Many organizations prefer to host Conda packages on private repositories rather than OSS repositories like the [Anaconda OSS Package Repository](repo.anaconda.com) to maintain control over security, compliance, and access management.

ProGet makes it easy to set up a private repository for your Conda packages to publish, store and share your packages internally.

This guide will show you how to set up a Conda package repository in ProGet and walk you through creating, publishing, and installing private packages for your team.

## Step 1: Create a New Feed

First, we will create a Conda feed to host your Conda packages. Start by selecting "Feeds" and "Create New Feed".

![New Feed](/resources/docs/proget-feeds-createnewfeed.png){height="" width="50%"}

Next, select "Conda Packages".

![Select Conda](/resources/docs/proget-conda-newfeed.png){height="" width="50%"}

Now select "No Connectors (Private packages only)" as we will be creating a private feed.

![No Connector](/resources/docs/proget-conda-newfeed-noconnector.png){height="" width="50%"}

From here, we name our feed. For this example, we will call it `internal-conda`, and then click "Create Feed".

![Name Feed](/resources/docs/proget-conda-newfeed-internal.png){height="" width="50%"}

We are then presented with several options. More information on these can be found in the [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) documentation.

![SCA Features](/resources/docs/proget-conda-internal-sca.png){height="" width="50%"}

Finally, we select [Set Feed Features], which will create the feed, and redirect us to our `internal-conda` feed, currently empty.

![Feed](/resources/docs/proget-conda-internal-emptyfeed.png){height="" width="50%"}

## Step 2: Create an API Key

We will now create an [API Key](/docs/proget/reference-api/proget-apikeys) allowing our local client to authenticate to our `internal-conda` feed and publish packages. 

Start by navigating to "Administration Overview" > "API Keys & Access Logs" under "Security & Authentication"

![Admin Overview](/resources/docs/proget-admin-apikeys.png){height="" width="50%"}

Then select "Create API Key"

![Create Key](/resources/docs/proget-apikey-new.png){height="" width="50%"}

Then fill in the fields by selecting "Feeds ("Use Certain Feeds)" as the "Feed Type" and selecting the `approved-conda` feed. Then set the API key. You can specify any alphanumeric sequence for this, or leave it blank to autogenerate one.

![New Key](/resources/docs/proget-conda-apikey-2.png){height="" width="50%"}

Ensure that the "View/Download" box is checked, and then select "Save".

## Step 3: Build Your Package

Next, we will build and publish our packages. You can follow the [official Conda documentation](https://docs.conda.io/projects/conda-build/en/latest/user-guide/tutorials/build-pkgs.html) to learn more about creating packages. To build your package you will need to have conda-build installed if you haven't already by entering:

```bash
$ conda install conda-build
```

Then build your package by navigating to the directory containing your package files and `meta.yaml` and entering:

```bash
$ conda-build .
```
 
When conda-build is finished, it displays the package filename and location of the `.tar.bz2` file created.

### Step 4: Publishing Your Package to ProGet

To publish your package to your ProGet Conda feed, we can use Inedo's [pgutil](/docs/proget/reference-api/proget-pgutil) tool.

pgutil will require some [minor configuration](/docs/proget/reference-api/proget-pgutil#sources) before use. This includes setting up your ProGet instance and API key as a source by running:

```bash
$ pgutil sources add --name=Default --url=«proget-url» --api-key=«api-key»
```

For example, adding the ProGet instance `https://proget.corp.local/` with the API Key `abc12345` you would enter:

```bash
$ pgutil sources add --name=Default --url=https://proget.corp.local/ --api-key=abc12345
```

Now upload your packages by entering:

```bash
$ pgutil packages upload --feed=«proget-url» --input-file=«path-to-package»
```

For example, uploading the package `my-package-0.1.0-0.tar.bz2` stored at `C:\development\conda_packages\` to your `internal-conda` feed you would enter:

```bash
$ pgutil packages upload --feed=«proget-url» --input-file==C:\development\conda_packages\my-package-0.1.0-0.tar.bz2
```

Your package will then be uploaded to the `internal-conda` feed.

![Feed](/resources/docs/proget-conda-internal-package.png){height="" width="50%"}