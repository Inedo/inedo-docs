---
title: "Other Feed Types"
order: 99
---

Although ProGet was designed from day one to support multiple types of third-party packages, it wasn’t until [ProGet 3.0](https://inedo.com/products/roadmap/proget) that we introduced support for feed types other than NuGet. Since then, we have added quite a few packages, and we plan to add many more.

This document explains the challenges of new feed types, how we support them, and the progress on requested feed types.

## Third-party package formats are not simple

We learned the hard way that many third-party package managers have an insanely complicated API that isn't only undocumented, but also requires a significant amount of reverse engineering just to understand how it might be implemented in a private repository.

This isn't a criticism per se, it's just that most of these third-party package managers were designed for community-built, free/open-source packages. This use case is completely different than a private repository like ProGet, which requires things like security beyond a simple API key, connectors, replication, license validation, etc.

For us, one of the biggest challenges with these third-party package types is that we lack expertise in most third-party package formats. For example, when it comes to developer packages, we're experts in C#/. NET (we use it to build our tools), so our engineers are very familiar with all the intricacies and complexities of NuGet. We definitely don't have that expertise in Java, Python, R, Perl, Rust, Ada, COBOL, or 99% of programming languages. This makes it quite difficult to learn the package managers that use these languages.

Supporting a new package format means we have to learn how those packages are used, learn the tools in the ecosystem, reverse engineer the package format, figure out the usage conventions, reverse engineer the APIs, etc. before we can even estimate how long it might take to engineer an architecture.

## Partnering with our users

Given the complexity of just understanding the basics of a third-party package format, we've found that the best way to implement new feed types is to work with our users. They're usually much more familiar with these package formats than we are, so they can tell us how to use them, point us to conventions, help us discover the internals, and finally try out a proof-of-concept. The last part (the trial and error) is really important, because we don't want to release a feed type that doesn't work... and we just don't know how to test it in real-world scenarios.

Without a user partnership, it's hard to support a new feed type. So this page will mainly serve as a place where we discuss the status of various requested third-party feed types.

## Status of new third-party feed types

Feel free to submit pull requests for this page or participate in the linked Q&A discussions if you have more information.

### ✔ Helm: Completed!

[Helm feeds](/docs/proget/feeds/helm) are available as of ProGet 5.2.

### ✔ Conda (Anaconda) Packages: Completed

 [Conda feeds](/docs/proget/feeds/conda) are available as of ProGet v6.0.6.
 
 ### ✔ Alpine (APK) Packages: Completed

 [Alpine (APK) feeds](/docs/proget/feeds/alpine) are available as of ProGet 2023.22.
 
### ✔ CRAN (R) Packages: Completed

 [CRAN (R) feeds](/docs/proget/feeds/cran) are available as of ProGet 2023.23.

### ✔ pub (Dart/Flutter): Completed

 [pub (Dart/Flutter) feeds](/docs/proget/feeds/pub) are available as of ProGet 2024.11.

### ✔ Terraform: Completed

[Terraform Feeds](/docs/proget/feeds/terraform) are available as of ProGet 2024.20.

### ✔ Cargo (Rust): Completed

[Cargo (Rust) Feeds](/docs/proget/feeds/cargo) are available as of ProGet 2024.20.


### ✔ PHP Composer/Packagist: Completed

[Composer (PHP) Feeds](/docs/proget/feeds/composer) are available as of ProGet 2024.22.

### ✔ Conan (C++): Completed

[Conan (C++) Feeds](/docs/proget/feeds/conan) are available as of ProGet 2024.22.

### 📉 WinGet: very limited demand

This is brand new from Microsoft, so there's not much demand for private feeds—but there is a [discussion on the forums](https://forums.inedo.com/topic/3257), so please contribute if you're interested.

### 📉Vagrant: very limited demand 

We've had three feature requests for Vagrant over the years, but they were casual inquiries and we didn't get any more info from those users. Our general feeling is that Vagrant is kind of on the outs, and containers are probably going to replace it. Hard to say. Share your thoughts by starting a thread in our [forums](https://forums.inedo.com/), and we'll link it here.

### 📉 Chef Cookbooks: very limited demand 

We've had one request for Chef cookbooks (with connectors to the Chef Supermarket) to date, but we know nothing about the underlying format. Interested? Share your thoughts by starting a thread in our [forums](https://forums.inedo.com/), and we'll link it here.

### 📉 Elixir Hex: very limited demand

We've had one request for Elixir Hex packages to date, but we know nothing about Elixir, Hex, or the underlying format. We're happy to learn though! Interested? Share your thoughts by starting a thread in our [forums](https://forums.inedo.com/), and we'll link it here.

### ❓ Generic OCI Registry: Just a "dumb" file system?

We've had [one request for a Generic OCI Registry](https://forums.inedo.com/topic/4256/oci-support). Our understanding is that an OCI registry is essentially a "dumb" file system that's built around "dumb" cloud storage like S3, etc. It *could* be used for technically anything, but in practice it's only used to store Docker images and Helm charts.

ProGet already has rich Docker and Helm chart support, and lets you see what charts relate to which containers, what packages containers use, the vulnerabilities, etc. Implementing an inferior means to store these doesn't seem like a good idea.

Of course, we could be wrong, so please contribute to the above-linked discussion to help us understand better!

### 🚫 CocoaPods: not possible

This is currently [in discussion on the forms](https://forums.inedo.com/topic/4224). Based on our initial technical assessment, it appears that a "CocoaPod" is not a package, but a convention for a Git repository.  In addition, there is no Server API or Repository API. It's all just Git repositories - and that would mean we'd not only need to develop a Git Server API, but implement the loosely-documented conventions.

Seems that just using a private Git repository to act as a CocoaPod repo is the only option.

### 🚫 Homebrew (MacOS): not possible
This is currently [in discussion on the forums](https://forums.inedo.com/topic/3306); technically this seems as impossible to do as CocoaPods, as Homebrew looks like it's just GitHub repositories. But moreover, there is no market.

### 🔎Go: package manager not found

We've had a few requests for these, but there doesn't seem to be a package management API. Behind the scenes, Go seems to use GitHub repositories as "packages", and if you want to privatize them, then you proxy github.org. We're not Go developers, so we could be wrong—let us know!

A few of our users simply use universal packages for Go libraries.

### 🔎 MCP Registry

This is currently [in discussion on the forums](https://forums.inedo.com/topic/5561); with GitHub Copilot we can provide and url for a MCP registry to use a [white list](https://docs.github.com/en/copilot/how-tos/administer-copilot/configure-mcp-server-access)
Exemple of the [A community driven registry service for Model Context Protocol (MCP) servers](https://github.com/modelcontextprotocol/registry)

### Other types?

These are the third-party that we know about but there are certainly more. Let us know by posting something in our [forums](https://forums.inedo.com/); we can link to it from this page, and track it fairly easily. 
