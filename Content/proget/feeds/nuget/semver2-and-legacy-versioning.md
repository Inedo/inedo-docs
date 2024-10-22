---
title: "NuGet Package Versioning (SemVer2, Legacy)"
order: 5
---

All NuGet packages—whether hosted on NuGet.org or in your own ProGet repository—have version numbers like `4.2.3`, `3.1.2.50012`, `6.2.0-beta1`, etc. The format of these version numbers is called a *version number scheme*, and NuGet has had several different schemes over the years.

This article helps explain how ProGet interacts with the different types of NuGet package version numbers.

## NuGet SemVer Package Versioning

Modern versions of ProGet (v5 and later) as well as NuGet (i.e. 4.3/2018+) support [SemVer2 versioning](https://semver.org/spec/v2.0.0.html) for packages. This is a standardized versioning scheme across many packages and has become intuitive to many software developers.

:::(Info) (Best Practices: Use SemVer2)
Use SemVer2-compatible versioning for your NuGet packages. We recommend it, Microsoft recommends it, and your future developers will appreciate it.
:::

A version number in SemVer2 format consists of three integer parts (major, minor, and patch) plus an optional suffix pre-release. Each component has a specific meaning:
* Major: Breaking changes
* Minor: New features, but backward compatible
* Patch: Backward-compatible bug fixes
* Prerelease: A non-stable package such as alpha, beta, etc.

Example versions include: `1.0.1`, `6.11.1231`, `4.3.1-rc`, `2.2.44-beta.1`.

See [Microsoft's NuGet Package Versioning Documentation](https://docs.microsoft.com/en-us/nuget/concepts/package-versioning){target="_blank"} to learn more about how to use version numbers in your packages.

### Build Metadata

There is a fifth, rarely used component in SemVer2: the build metadata. It starts with a plus sign(`+`) and may contain any of the characters that are allowed to contain suffixes for pre-release versions. Build metadata is ignored when comparing version numbers, so `1.2.3+foo` and `1.2.3+bar` are considered the same version.

In addition, NuGet generally ignores build metadata when it requests packages (e.g., if you specify `1.2.3+foo`, NuGet only asks for `1.2.3`);  thus, ProGet generally doesn't require build metadata when it requests packages. We say "in general" because there are bugs and quirks in different versions of NuGet (and also of ProGet).

:::(Info) (Best Practices: Avoid Build Metadata in NuGet Packages)
It's not very useful in NuGet, causes confusion, and you can just add additional metadata to your .nuspec if you need it.
:::

## Legacy NuGet Version Numbers

Prior to supporting SemVer2 versioning, NuGet used a 4-digit versioning scheme (e.g. `3.4.1.5`) that is a combination of [.NET's System.Version class](https://docs.microsoft.com/en-us/dotnet/api/system.version) and SemVer1 (an older, obsolete specification). This format isn't very well documented.

ProGet considers these "legacy version numbers" and defines them as:
* four decimal integers (digits`0-9`), separated by periods (`.`)
* optionally a pre-release indicator, i.e. a hyphen (-) followed by a sequence of letters (a-z, A-Z), digits (0-9), and hyphens (-). Pre-release indicators must be at least one character long and begin with a letter.

Modern versions of ProGet and NuGet still fully support these types of version numbers.

## Quirky Version Numbers

Ancient versions of NuGet (i.e. prior to 3.4/2016) had very few rules, and the rules changed from version to version of NuGet.  Versions could be 1, 2, 3, or 4 parts. In addition, the equality rules were a bit strange. For example, `1.0`, `1.000`, and `1.0.0` were considered different version numbers in most cases... except when they weren't. Likewise, `6.4` and `6.04` were almost always different versions, depending on how you requested them from NuGet. 

We call these "quirky version numbers", and fortunately they have not been allowed on nuget.org since 2016. Unfortunately, there are many packages with "quirky version numbers" created before 2016 that people still want to use.

A great example of a quirky package is [Owin](https://www.nuget.org/packages/Owin). There's a single version (`1.0`), and it hasn't changed since 2012. It's considered a "quirky version" because it's only a two-part number.

### NuGet.org and Quirky Versions

It's not possible to upload packages in quirky versions to nuget.org, but the old packages still exist.

NuGet.org now only shows a "normalized", three-part version number for quirky packages. Even if the package version (as specified in the `.nuspec` file) is `1.0`, nuget.org will return `1.0.0` as the version number in the API. 

You'll notice that `Owin` is listed as `1.0.0`, even though the *actual* version is `1.0`. The only way to see the *actual* version is to download the file, and look in the `.nuspec` file:

```xml
  <metadata>
    <id>Owin</id>
    <version>1.0</version>
    ...snip...
  </metadata>
```

However, you still request `Owin-1.0` by name, directly. NuGet.org will return the one (and only) version of the `Owin` package.

### NuGet Client Support for Quirky Versions

Since not all NuGet servers behave like NuGet.org, the NuGet client (Visual Studio) has to do some interesting workarounds, including "version dancing", to make sure it can find a requested package.

For example, if a package has a dependency on `Owin-1.0`, then you'll see NuGet make multiple requests to the package source: first `1.0.0`, then `1.0.0.0`, then `1.0`, and finally `1`.

This allows the NuGet client to work with both nuget.org, and legacy NuGet servers.

### ProGet Support for Quirky Versions

You can still upload a "quirky version" of packages to ProGet in many cases, and these quirky packages will work in most cases. However, they can cause headaches, especially when it comes to connectors and operations around local packages (like promotion and repackaging).

For example, `Owin` will show up as `1.0.0` via a connector to NuGet.org, because that's what NuGet.org is reporting. In most cases, it will work just fine, even though it's *really* `1.0` behind the scenes.

However, when you pull `Owin` to ProGet, ProGet will use the "true" version number (`1.0`). It will *also* show `Owin 1.0.0`.

Old versions (before 5.3) had a "Legacy (Quirks) Nuget" feed that could handle all these oddities, but those feeds couldn't handle SemVer queries properly because queries like 1.0 were ambiguous (should 1.0 or 1.0.0 be returned, which are both valid versions, etc.), so it was a big compromise. They have been completely removed in ProGet 5.3.

Because there are very few packages with quirks like this, we recommend simply downloading the package, editing the `.nuspec` file, and replacing it with a semantic version. 