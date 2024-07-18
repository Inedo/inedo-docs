---
title: "Repackaging & Auditing"
order: 4
---


Pre-release makes it clear to everyone which packages are of release quality and which are not. As with any software, it is important to create several pre-release versions that are tested and validated before creating the final, stable package. You can use different pre-release versions for this purpose; for example:
- `ProfitCalc 3.2.4-ci.54` may signify a package created on ci server (build # 54).
- `ProfitCalc 3.2.4-beta.2` may signify a second beta version that customers can test.
- `ProfitCalc 3.2.4-rc.1` may signify a package is ready for final release, pending some last-minute testing.
- `ProfitCalc 3.2.41` is the production, release-quality package.

This versioning methodology is something that both the business and development can understand. Of course, there's just one small technical problem: How do you know when to create CI, beta, release candidate, and stable packages?  It's impossible to know. This is where **Repackaging** comes in.

## From Prerelease to Stable
There are some basic rules for packaging and software development that our industry tends to follow closely.
1. Packages are immutable. Once released, a package file should never be changed. This means that you cannot "edit" the version number of a package because the version number is part of the metadata embedded in the file.
2. Software should never be deployed in a production environment without first being thoroughly tested.
3. Rebuilding source code may result in different software. We talked about _dependency hell_ in chapter three, but it's also possible that a different version of the compiler will treat the source code differently.
4. A prerelease package should never be released.

These are discussed at length in our book [Continuously Scale and Continuously Deliver with a Universal Package Manager](https://inedo.com/support/resources/ebooks/continuously-scale-deliver-upm).

Given these rules, it seems almost impossible to create a logical pipeline that both the business and development can understand and follow.

This is where repackaging comes in. Repackaging is a process of creating a new package with exactly the same content from an existing package, while maintaining the integrity of the original package. A very secure workflow must be followed to prevent tampering with the content, and at the same time, an audit trail must be created to prove that the work was done according to policy.

The universal packaging [manifest file](/docs/proget/upack/upack-universal-packages/upack-universal-packages-manifest) allows for storing a chain of repackaging events that allow you to verify each preceding package. [ProGet](/docs/proget/packages/repackaging) also supports this as a feature, and eliminates the complex, tedious manual steps involved in repackaging, and ensures security.


<iframe width="600" height="337" src="https://www.youtube.com/embed/BYfWcm7tppM" frameborder="0" allowfullscreen="true"></iframe>