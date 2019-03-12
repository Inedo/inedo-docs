---
title: Package Identification
subtitle: Package Identification
sequence: 20
keywords: proget, universal-packages
---
A universal package can be uniquely identified it's group, name, and version. These are different properties in the [manifest file](/support/documentation/upack/universal-packages/metacontent-guidance/manifest-specification).

<iframe width="600" height="337" src="https://www.youtube.com/embed/kHYgicz5Bec" frameborder="0" allowfullscreen="true"></iframe>

In some cases, you will need to refer to a package with a single string. In this case, the group and name are combined into a single string, separated by a forward-slash, and the version is followed (separated by a colon). If a package is not in a group, then it's considered in the *empty* group, and that slash is not used. For example:

{.docs}
- ```HDARS:1.3.9``` is version 1.3.9 of the HDARS package (which is in the empty group)
- ```initrode/vendors/abl/ABLast:2.2.1-rc.1``` is rc.1 prerelease of v2.2.1 of ABLast in the "initrode/vendors/abl" group

:::attention {.best-practice}
**Note:** you should avoid really generic names for your packages, like "Utils" or "Tool". Instead, consider InitechUtils or InitrodeTool.
:::

You may also append a colon and then a hash string to the end of a package. This will be used for verification purposes when possible. For example:

{.docs}
- ```HDARS:1.3.9:fca66ce2a8ceea2d651eecf2369d4072d1871aec```
- ```initrode/vendors/abl/ABLast:2.2.1-rc.1:5b31eaa26d0c6e7bb985f740dbceed854293c369```
