---
title: Other Feed Types
sequence: 1000
keywords: proget,feeds
---
Although ProGet was designed from day one to support multiple types of third-party packages, it wasn’t until [ProGet 3.0](https://inedo.com/products/roadmap/proget) that we introduced support for feed types other than NuGet. Since then, we’ve added a whole bunch, and we want to add a whole lot more.

This document will walk through the challenges of new feed types, how we support them, and the progress of requested feed types.

## Third-party packages formats are not simple

We’ve learned the hard way that many third-party package managers have an insanely complicated API that’s not only undocumented but requires a significant amount of reverse engineering just understand how it might be implemented in a private repository. 

This is not a criticism per se, it just that most of these third-party package managers were designed for community-built, free/open source packages. This use case is totally different than a private repository like ProGet, which requires things like security outside of a basic API key, connectors, replication, license validation, etc.

For us, one of the biggest challenges with these third-party package types is that we lack domain expertise in most third-party package formats. For example, when it comes to developer packages, we are experts on C#/.NET (it’s what we use it to build our tools), so our engineers are going to be intimately familiar with all of the intricacies and complexities of NuGet. We definitely don’t have that expertise in Java, Python, R, Perl, Rust, Ada, COBOL, or 99% of programming languages. That makes it pretty tough to learn the package managers those languages use.

Supporting a new package format means we need to learn how these packages are used, learn the tools in the ecosystem, reverse engineer the package format, figure out the usage conventions, reverse engineer the APIs, etc., before we can even assess how long it might take to engineer an architecture. 


## Partnering with our users

Given the complexity with just understanding the basics of a third-party package format, we’ve found the best way to implement new feed types is by partnering with our users. They tend to be much more familiar with these package formats than we are, so they can tell us how to use them, point us to conventions, help us discover the internals, and eventually try a proof-of-concept.  That last part (trying it out) is really important, because we don’t want to release a feed type that doesn’t work… and we just don’t know how to test it in real-world scenarios. 

Without a user partnership, getting a new feed type supported is challenging. As such, this page will largely serve as the place where we discuss the status of various third-party feed types that have been requested. 


## Status of new third-party feed types

You’re welcome to submit pull requests to this page or contribute to the linked Q&A discussions if you have more information.

### Helm: Completed!

Thanks to the investigative work of Scott Cusson (one of our users), we were able to implement Helm charts in ProGet as a proof-of-concept in a matter of days. As it turned out, a private Helm chart repository could almost entirely be implemented as an Asset Directory (Scott had tried this on his own); ProGet just needed to generate a special Metadata file to list the packages in the directory for this to work via the Helm client. Once we knew that, it was pretty easy for us to dig in and figure out how to implement this feed type. It was shipped in ProGet 5.2 thanks to Scott and his team’s help.


### RPM: Now in Pre-release!

Thanks to support and investigation from the user community, we were able to publish this as a pre-release already! We will soon integrate this into a maintenance release; see [QA#9887](https://inedo.com/support/questions/4045) to join in the discussion.

### R and CRAN: Ready for development, once we find user partners

We've done our initial research, and have a pretty good idea of how to develop this now. But need your help to make sure it's done right. Please join the discussion at [QA#2686](https://forums.inedo.com/topic/2686/support-for-r-and-cran) to learn more and help us develop this feed type!


### PHP Composer/Packagist: Evaluated, seems really tough

We did a pretty deep dive into PHP/Composer packages a while back, and our conclusion was that they were very difficult to implement due to the way the tightly integrate with git repositories. 

However, we did this assessment without any user partners, and we know next to nothing about PHP, so it could be we misunderstood or looked at the wrong things. Maybe not everyone uses the tight git-repository integration? Hard to say. This is why we partner with customers now.

Since then, there haven’t been too many requests for it, and we have no idea what the level of interest is for. Please add to [QA#2690](https://inedo.com/support/questions/2690) if you've got some insight.

### Vagrant: very limited demand 

We've had three feature requests for Vagrant over the years, but they were fairly  casual inquiries and we didn't get any more info from those users. Our general feeling is that Vagrant is kind of on the outs, and containers are probably going to replace it. Hard to say. Share your thoughts by starting a thread in our [forums](https://forums.inedo.com/), and we'll link it here.

### Chef Cookbooks: very limited demand 

We've had one request for Chef cookbooks (with connectors to the Chef Supermarket) to date, but we know nothing about the underlying format. Interested? Share your thoughts by starting a thread in our [forums](https://forums.inedo.com/), and we'll link it here.

### Conda (Anaconda) Packages: probably not

We've had some interest in [Conda packages](https://docs.conda.io/en/latest/), and wrote up our thoguhts in the community forums.  Please add to [QA#2901](https://forums.inedo.com/topic/2901) to share your thoughts!


### CocoaPods: no actual interest

We've had one customer ask about this as a feature request; after some back-and-forths it turns out that one of the customer's development teams said "they might consider a private package manager", but didn't really care one ay or another. 

### Go: package manager not found

We have a lot of users who use Go, and they've told us there is no "proper" package management solution, so no sense in implementing it. Some use universal packages. 

### Other types?

These are the third-party that we know about, but there are certainly more. Just let us know by posting something in our [forums](https://forums.inedo.com/); we can link to it from this page, and track it fairly easily then. 

