---
title: "Universal Package & Feed Reference"
order: 6
hidden: true
---

Universal Package Feeds were designed to provide immediate, out-of-the-box functionality for most packaging needs, and also to serve as a platform that you can easily extend or interact with.

:::(INFO)
**Shared Documentation Note** - instead of creating a whole new set of documentation for Universal Packages and Feeds we've kept the detail pages in the Various Documentation, and the ProGet specific things are noted on these pages:
  - [Universal Feed API & Package Reference](/docs/proget/upack/upack-overview)
  - [Universal Package Registry](/docs/proget/upack/upack-universal-package-registry/upack-universal-package-registry-what-is)
  - [UPack Command-line Tool](/docs/proget/upack/upack-tools-and-libraries/upack-tools-and-libraries-upack-cli)
:::

#### UPack Command-line Tool 

*upack.exe* is a standalone command-line tool to aide in the creation of universal packages, and is typically used on build servers or in other automation scenarios.

<iframe width="600" height="337" src="https://www.youtube.com/embed/blGLta0cvPA" frameborder="0" allowfullscreen="true"></iframe>

#### Universal Package Registry 

[Universal Feeds and Packages](/docs/proget/packages/what-is-a-package) are "lightweight" and, on their own, have very few built-in features. This design has allowed them to be utilized for all sorts of packaging problems such as application delivery, Inedo's product extensions, and even private Bower packages.

  When developing a package-based solution, one question that often comes up is "what packages are installed or used in this particular context?" That's where the Universal Package Registry comes in; it's a local package registry designed specifically for Universal Packages.