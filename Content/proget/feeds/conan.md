---
title: "Conan (C++)"
order: 21
---

[Conan](https://conan.io) is a binary management system for C++ that allows retrieval of precompiled binaries usable by different compilers and platforms, as well as the ability to build from sources when a precompiled binary is not available.

While Conan does not have self-describing package files like most packaging systems, its recipes are roughly analogous to packages in ProGet.

A Conan feed in ProGet acts like a private Conan repository where you can store your own recipes (and their packages). You can also create connectors to [Conan Center](https://center.conan.io) and other Conan V2 repositories that let you use third-party recipes through your Conan feed and create a curated list of approved recipes.

Conan feeds are available in ProGet 2024.22+.

## Adding a Conan feed as a Remote
Before installing or publishing a crate to your ProGet Conan feed, you will need to add your feed as a remote:

```bash
conan remote add «remote-name» https://«proget-server»/conan/«feed-name»/
```

If you are using a self-signed certificate with ProGet, you will also need to add the `--insecure` argument:

```bash
conan remote add «remote-name» https://«proget-server»/conan/«feed-name»/ --insecure
```

### Proxy All Conan Requests Through ProGet

When you add another remote to Conan, it will still use [Conan Center](https://center.conan.io) unless you explicitly disable it. To proxy all requests through your ProGet feed instead, make sure you disable Conan Center:

```bash
conan remote disable conancenter
```

### Authenticated Feeds

If your require authentication, Conan will prompt for login credentials, or you can explicitly log in using the `conan remote auth` command.

Conan prompts for a user name and password for authentication. You may use your ProGet user name and password, or you may specify `api` as the user name and use an API key or personal access token.


## Creating and Publishing Packages

Unlike many other packaging system, Conan does not have self-contained archive files. Instead, you use Conan to create a package in your local cache, and then you publish that recipe to an external repository like ProGet.

The best way to become familiar with Conan's unique model is to follow the [Creating Packages tutorial](https://docs.conan.io/2/tutorial/creating_packages.html) in the documentation.


### Publishing Packages to ProGet

Once you have created a package in your local cache, you can publish it to ProGet using the `conan upload` command:

```bash
conan upload -r «remote-name» «package-name»
```

Due to the complexity of the Conan package model, you cannot use any other tools to upload packages, only the Conan CLI.


## Technical Limitations

 - ProGet only implements the Conan V2 API. Conan V1 has been deprecated for some time and is no longer supported by the Conan CLI.
 - The Conan API does not have the capability to list metadata remotely, so ProGet does not show and connector results while browsing Conan feeds in the ProGet UI. However, connectors fully support proxying or caching, so if you have your feed configured to cache connector packages, they will be added to your feed as they are used.
 - Since Conan doesn't exactly have package files, the drop path import function only works with files stored in a very specific directory structure that mimics how ProGet stores Conan files - this allows you to import from another ProGet feed or instance if necessary.
