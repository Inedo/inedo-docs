---
title: Prerelease Packages & Repackaging
subtitle: Prerelease Packages & Repackaging
keywords: packaging, repackaging, upack, universal-packages, proget
sequence: 10
---

- Through prerelease versioning, it's clear to everyone which packages are release-quality and what is not ready for release. 
- Like all software, it is important to create several prerelease packages that are tested and validated prior to creating the final, stable package.
- You can use different prerelease versions for this purpose; for example
  - `ProfitCalc 3.2.4-ci.54` may signify a package created on ci server (build # 54).
  - `ProfitCalc 3.2.4-beta.2` may signify a second beta version that customers may test.
  - `ProfitCalc 3.2.4-rc.1` may signify a package is ready for final release, pending some last-minute testing.
  - `ProfitCalc 3.2.41` is the production, release-quality package.
    
- This versioning methodology is something that both the business and development can understand. Of course, there's just one small technical problem: How do you know when to create CI, beta, release candidate, and stable packages? 
- It's impossible to know. This is where repackaging comes in.

## From Prerelease to Stable
First, a quick review on the some basic rules about packaging and software development; these are discussed at length in our book [Continuously Scale and Continuously Deliver with a Universal Package Manager](i requested link from rachel..  will be pubished soon.)

1. Packages are immutable. Once published, a package file should never be modified. This means you can't "edit" a version number of a package because the version number is part of the metadata embedded in the file.
2. Software should never be deployed to a production environment without being tested thoroughly. 
3. Rebuilding source code can produce different software. We talked about _dependency hell_ in chapter three, but it’s also possible that a different version of the compiler will treat source code differently.
4. You can't release a prerelease package. This is just common sense.

Given these rules, it seems nearly impossible to create a logical pipeline that both the business and development can understand and follow.

This is where repackaging comes in. It's a process where you create a new package from an existing package using exactly the same content. This will also need to follow a secure work flow to prevent content tampering, while generating an audit trail that proves it.

## Repackaging a Package
ProGet allows users to do this at the click of a button or with a [simple API call](https://inedo.com/support/documentation/proget/reference/api/repackaging). 

To use the ProGet web interface:
1. Browse to any universal package in ProGet. 
2. Click on the the "Repackage" button found under _Local Operations_.
3. Enter a new version number, and you may also optionally add a reason (for auditing purposes)
4. Click "Repackage"

Once you've done that, you'll be redirected to the the new package's page.

## Viewing and Auditing Repackage History
- You can see the audit trail on the "History" tab of the package.
- The date, time, version, user name, notes, etc. are maintained, and if the orginal package is in ProGet you can access it from this page.
- These historic audit trails are embedded in the package itself, which allows you to easily replicate or promote this package to other feeds and the history will be preserved.

## Other Repackaging Options
- **Via API**: you can use the [api](https://inedo.com/support/documentation/proget/reference/api/repackaging) to repackage a package.
- **Via Command Line**: you can use [upack.exe](https://inedo.com/support/documentation/upack/tools-and-libraries/upack-cli) to repackage a package. 