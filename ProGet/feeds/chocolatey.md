---
title: Chocolatey (Windows/Machine)
subtitle: Chocolatey Feeds in ProGet
sequence: 500
keywords: proget,feeds,chocolatey
show-headings-in-nav: true
---

[Chocolatey](https://chocolatey.org/) is a package manager for Windows. Similar to Linux tools like apt or yum, but designed specifically for Windows, Chocolatey was designed to be a decentralized framework for quickly installing applications and tools that you need.

### Installing Packages

Chocolatey packages are installed using [Chocolatey](https://chocolatey.org/docs/commands-install). To install a package from a ProGet feed, use the following command:

```
choco install {package-name} --version {package-version} --source http://{proget-server}/nuget/{feed-name}/
```

### Creating Packages

To learn how to create a package that can be hosted by ProGet, visit the [Creating Chocolatey Packages](https://chocolatey.org/docs/create-packages). 


## Differences from NuGet Feeds {#nuget-feeds data-title="Differences from NuGet Feeds"}

Since Chocolatey is built on the NuGet infrastructure, a Chocolatey feed in ProGet is actually a type of [NuGet](nuget) feed. Functionally, it behaves exactly the same as a NuGet feed, but the user interface is optimized for Chocolatey packages:

{.docs}
-   Packages in Chocolatey feeds have a different icon to indicate that they are intended for use by Chocolatey
-   Package install instructions also provide an example for installing the package using `choco` instead of NuGet

In the video below, Chocolatey founder Rob Reynolds presents a demo of ProGet and Chocolatey, reviewing how both come together to provide a complete Windows package management solution.

<iframe width="600" height="337" src="https://www.youtube.com/embed/BcTYGf7sQ8Q" frameborder="0" allowfullscreen="true"></iframe>
