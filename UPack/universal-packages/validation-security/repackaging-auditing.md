---
title: Repackaging & Auditing
subtitle: Repackaging & Auditing
sequence: 10
keywords: proget, universal-packages
---
Packages work at their best when they are read-only and cryptographically verified, but this means that you can't change any of the package metadata (including the version number) without "tampering" with the package.

:::attention {.best-practice}
![](/resources/images/icons/best-practices.png) For example, if you have a pre-release package (HDars.Web:1.4.5-rc.2), but just want to "change" the version, then you will have created a new package. If you change a different piece of metadata (such as the author name), then you've created a very confusing situation where two different files are apparently the same package.
:::

This is where repackaging comes in: it's an operation that involves changing a small part of package metadata (such as the version number)  without altering the contents, while retaining a "pointer" to the original package inside the newly created package. Of course, this operation can only be performed by a trusted person or service to ensure that the repackaging is securely performed.

<iframe width="600" height="337" src="https://www.youtube.com/embed/BYfWcm7tppM" frameborder="0" allowfullscreen="true"></iframe>

The universal packaging [manifest file](/support/documentation/upack/universal-packages/metacontent-guidance/manifest-specification) allows for storing a chain of repackaging events that allow you to verify each preceding package.
