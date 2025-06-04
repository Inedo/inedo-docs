---
title: "HOWTO: Create and Publish Conan Packages to a Private Repository in ProGet"
order: 2
---

ProGet lets you set up private repositories for your [Conan](https://conan.io) recipes/packages, allowing you to upload them from a local source, store, and install them internally.

In this article we will cover how to create a ["Feed"](/docs/proget/feeds/feed-overview) in ProGet to act as a private Conan package repository, as well as covering how to create, publish, and install packages from this feed.

## Step 1: Create a New Feed { #create-feed }

First, we will create a feed to host your Conan packages. In ProGet, select "Feeds" and "Create New Feed". Then, select "Conan Packages" under "Developer Libraries".

![Create New Feed](/resources/docs/proget-feeds-conanselect.png){height="" width="50%"}

Then select "No Connectors (private recipes only)" as we will be creating a private feed. Now name your feed. For this example we will call it `internal-conan`.

![Name Feed](/resources/docs/proget-conan-nameinternal.png){height="" width="50%"}

You will then see options that relate to ProGet's [Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities) features, however as they are only for users looking to use third party packages, leave these boxes unchecked and select [Set Feed Features]. You will then be redirected to the newly created, empty `internal-conan` feed.

![Private Conan Feed](/resources/docs/proget-conan-emptyinternal.png){height="" width="50%"}

## Step 2: Create Your Conan Package { #create-package }

Next, we will create a Conan package. You can follow the tutorial in the [official Conan documentation](https://docs.conan.io/2/tutorial/consuming_packages/build_simple_cmake_project.html) to learn about creating these in more detail.

Navigate to your project directory, and create a file named `conanfile.py`. This will contain content similar to the following example:

```python
from conan import ConanFile
from conan.tools.files import copy
import os

class MyPackage(ConanFile):

    name = "mypackage"
    version = "1.2.3"
    settings = "os", "arch", "compiler", "build_type"
    description = "A simple Conan package example"
    license = "MIT"
    author = "Your Name <youremail@example.com>"
    url = "https://example.com"
    topics = ("example", "conan", "tutorial")

    def package(self):
        src = os.path.join(self.source_folder, "src")
        dst = os.path.join(self.package_folder, "include")
        copy(self, "*", src=src, dst=dst)

    def package_info(self):
        self.cpp_info.includedirs = ["include"]
```

Then create a sub directory named `src` and place your project files here.

Finally, run the following command to export your package to the local cache:

```bash
$ conan create . --name=mypackage --version=1.2.3
```

## Step 3: Create an API Key

Now you will need to create an [API Key](/docs/proget/api/apikeys) to let your Conan client authenticate to the `internal-conan` feed. This will let you publish and install packages from that feed. 

:::(info) (Authenticating with an API Key)
While you _can_ authenticate with a ProGet username and password, we highly recommend using an API Key instead. For that, use `api` as the username and the API Key as the password.
:::

When creating an API Key, fill in the fields by selecting "Feeds (Use Certain Feeds)" as the "Feed Type" and selecting the `internal-conan` feed. Then set the API key. You can use any alphanumeric sequence, or just leave it blank to autogenerate one. Ensure that the "View/Download" and "Add/Repackage" boxes are checked, and then select "Save".

![Create API Key](/resources/docs/proget-conan-api-internal.png){height="" width="50%"}

## Step 4: Configure Your Conan Feed as a Remote { #add-feed }

To upload packages to `internal-conan` feed from your Conan V2 client (as well as install any packages uploaded to it), you will need to add it as a remote using the [`conan remote add`](https://docs.conan.io/1/reference/commands/misc/remote.html) command. 

```bash
$ conan remote add internal-conan https://proget.corp.local/conan/internal-conan/
```

If you are using a self-signed certificate with ProGet, you will also need to add the `--insecure` argument:

```bash
$ conan remote add internal-conan https://proget.corp.local/conan/internal-conan/ --insecure
```

### Recommended: Removing Conan Center as a Remote

By default the Conan client will have [Conan Center](https://center.conan.io) configured as a remote unless you explicitly disable it. We recommend disabling Conan Center to make sure all requests are exclusively made to your `internal-conan` feed, and developers aren't installing packages directly from Conan Center. 

```bash
$ conan remote disable conancenter
```

## Step 5: Upload Your Package to ProGet { #upload-package }

To publish your Conan package to your `internal-conan` ProGet feed, use the [`conan upload`](https://docs.conan.io/1/reference/commands/creator/upload.html) command:

```bash
$ conan upload -r internal-conan my_package
```

:::(info)(Uploading Packages)
Packages can only be uploaded to ProGet using the Conan CLI due to the complexity of the Conan package model
:::

Your package will then be uploaded to ProGet:

![Uploaded Package](/resources/docs/proget-conan-internalpackage.png){height="" width="50%"}

## Step 6: Using your Conan Feed as a Remote to Install Packages

:::(warn)(Build Profile)
Before installing packages you will need to configure a build profile in your Conan V2 client. You can create a default profile with the [`conan profile detect`](https://docs.conan.io/2/reference/commands/profile.html) command or specify your own profile with `--profile:build=«myprofile»`
:::

To install Conan packages from your `internal-conan` feed, use the [`install`](https://docs.conan.io/1/reference/commands/consumer/install.html) command:

```bash
$ conan install --requires=mypackage/1.2.3 -r=internal-conan
```

## (Optional) Installing Packages from an Authenticated Feed
By default, your `internal-conan` feed is configured so that packages can be installed from it anonymously. However, if you have set up authentication for your feed, you will need to [authenticate to it](/docs/proget/feeds/conan#authenticate-to-feed). You can use a ProGet `username` and `password`, however we highly recommend [Creating a ProGet API Key](/docs/proget/api/apikeys) for authentication, using `api` as the username and the API key as the password. 