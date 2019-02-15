---
title: Chocolatey (Windows/Machine)
subtitle: Chocolatey Feeds in ProGet
sequence: 500
keywords: proget,feeds,chocolatey
show-headings-in-nav: true
---

[Chocolatey](https://chocolatey.org/) is a package manager for Windows. Similar to Linux tools like apt or yum, but designed specifically for Windows, Chocolatey was designed to be a decentralized framework for quickly installing applications and tools that you need.

## Differences from NuGet Feeds {#nuget-feeds data-title="Differences from NuGet Feeds"}

Since Chocolatey is built on the NuGet infrastructure, a Chocolatey feed in ProGet is actually a type of [NuGet](nuget) feed. Functionally, it behaves exactly the same as a NuGet feed, but the user interface is optimized for Chocolatey packages:

{.docs}
-   Packages in Chocolatey feeds have a different icon to indicate that they are intended for use by Chocolatey
-   Package install instructions also provide an example for installing the package using `choco` instead of NuGet

In the video below, Chocolatey founder Rob Reynolds presents a demo of ProGet and Chocolatey, reviewing how both come together to provide a complete Windows package management solution.

[![](http://img.youtube.com/vi/BcTYGf7sQ8Q/0.jpg)](http://www.youtube.com/watch?v=BcTYGf7sQ8Q "")
