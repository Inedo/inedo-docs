---
title: "HOWTO: Upload Python Packages to a Private PyPI Repository in ProGet"
order: 2
---

With ProGet you can set up private repositories for your Python packages, so you can publish, store, and share them internally.

This article will run through how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet to act as a private Python package repository, as well as covering how to create, publish, and install packages from this feed. This page provides instructions when using pip, however you can also integrate other tools such as [PipEnv and Poetry](/docs/proget/feeds/pypi/integrate-pypi-others) with your PyPI feeds. 

## Step 1: Create a New Feed

To start with, we will create a feed to host your Python packages. In ProGet, select "Feeds" and "Create New Feed". Then, select "Python Packages" under "Developer Libraries".

![](/resources/docs/proget-pypi-createfeed.png){height="" width="50%"}

Then select "No Connectors (Private packages only)" as we will be creating a private feed. Now name your feed. For this example we will call it `internal-pypi`.

![](/resources/docs/proget-pypi-internal-namefeed.png){height="" width="50%"}

You are then presented with several options. These relate to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features, however they are only for users looking to use third party packages. Leave these boxes unchecked, and select [Set Feed Features]. You will then be redirected to your new `internal-pypi` feed, currently empty.

![](/resources/docs/proget-pypi-internal-empty.png){height="" width="50%"}

## Step 2: Create Your Python Package { #create-package }

Next, we will create our Python packages. You can follow the [official Python documentation](https://packaging.python.org/en/latest/tutorials/packaging-projects/) to learn more about creating these. Before you create a package you will need to have `setuptools` installed by running `pip install setuptools wheel`

To create a Python package you will need a folder with the necessary project files, including `setup.py`, `README.md`, and `__init__.py`. The a project structure will typically look like this:

```plaintext
my_package/
│
├── my_package/
│   ├── __init__.py
│   ├── python_module.py

├── setup.py
├── README.md
```

Then build your package by navigating to the directory containing `setup.py` and entering:

```bash
python setup.py sdist bdist_wheel  
```

This will create two directories: `dist/` and `build/`, with `.tar.gz` (e.g. `my_package-1.0.0.tar.gz`) and `.whl` (e.g. `my_package-1.0.0-py3-none-any.whl`)files inside `dist/`.

## Step 3: Create an API Key

Now you will need to create an [API Key](/docs/proget/reference-api/proget-apikeys) to let local Python clients authenticate to the `internal-pypi` feed. This will let you publish and access packages from that feed. 

:::(info) (Authenticating with an API Key)
While you _can_ authenticate with a ProGet username and password, we highly recommend using an API Key instead. For that, use `api` as the username and the API Key as the password.
:::

When creating an API Key, fill in the fields by selecting "Feeds (Use Certain Feeds)" as the "Feed Type" and selecting the `internal-pypi` feed. Then set the API key. You can use any alphanumeric sequence, or just leave it blank to autogenerate one. Ensure that the "View/Download" and "Add/Repackage" boxes are checked, and then select "Save".

![New Key](/resources/docs/proget-pypi-api.png){height="" width="50%"}

## Step 4: Upload Your Package to ProGet { #upload-package }

To publish your Conan package to your `internal-conan` ProGet feed, use the [`conan upload`](https://docs.conan.io/1/reference/commands/creator/upload.html) command:

```bash
$ conan upload -r «remote-name» «package-name»
```

:::(info)(Uploading Packages)
Packages can only be uploaded to ProGet using the Conan CLI due to the complexity of the Conan package model
:::

Your package will then be uploaded to ProGet:

![](){height="" width="50%"}

## Step 5: Using your Conan Feed as a Source to Install Packages



## (Optional) Installing Packages from an Authenticated Feed
By default, your `internal-conan` feed is configured so that packages can be installed from it anonymously. However, if you have set up authentication for your feed, you will need to [authenticate to it](/docs/proget/feeds/pypi#authenticating-to-a-pypi-feed). You can use a ProGet `username` and `password`, however we highly recommend [Creating a ProGet API Key](/docs/proget/reference-api/proget-apikeys) for authentication, using `api` as the username and the API key as the password. 