---
title: Repackaging & Auditing
sequence: 40
keywords: proget, universal-packages
---

Through prerelease versioning, it's clear to everyone which packages are release-quality and what is not ready for release. Like all software, it is important to create several prerelease packages that are tested and validated prior to creating the final, stable package. You can use different prerelease versions for this purpose; for example
- `ProfitCalc 3.2.4-ci.54` may signify a package created on ci server (build # 54).
- `ProfitCalc 3.2.4-beta.2` may signify a second beta version that customers may test.
- `ProfitCalc 3.2.4-rc.1` may signify a package is ready for final release, pending some last-minute testing.
- `ProfitCalc 3.2.41` is the production, release-quality package.

This versioning methodology is something that both the business and development can understand. Of course, there's just one small technical problem: How do you know when to create CI, beta, release candidate, and stable packages?  It's impossible to know. This is where **Repackaging** comes in.

## From Prerelease to Stable
There are some basic rules regarding packaging and software development that our industry tends to follow closely.
1. Packages are immutable. Once published, a package file should never be modified. This means you can't "edit" a version number of a package because the version number is part of the metadata embedded in the file.
2. Software should never be deployed to a production environment without being tested thoroughly.
3. Rebuilding source code can produce different software. We talked about _dependency hell_ in chapter three, but it's also possible that a different version of the compiler will treat source code differently.
4. A prerelease package should never be released.

These are discussed at length in our book [Continuously Scale and Continuously Deliver with a Universal Package Manager](https://inedo.com/support/resources/ebooks/continuously-scale-deliver-upm).

Given these rules, it seems nearly impossible to create a logical pipeline that both the business and development can understand and follow.

This is where repackaging comes in. Repackaging is process where that creates a new package from an existing package using exactly the same content while maintaining the integrity of the original package. This also needs to adhere to a very secure work flow to prevent content tampering, while also generating an audit trail that proves it was done according to the guidelines.

The universal packaging [manifest file](manifest) allows for storing a chain of repackaging events that allow you to verify each preceding package. [ProGet](/docs/proget/advanced/repackaging) also supports this as a feature, and eliminates the complex and tedious manual steps involved in repackaging, and ensures security.


<iframe width="600" height="337" src="https://www.youtube.com/embed/BYfWcm7tppM" frameborder="0" allowfullscreen="true"></iframe>
