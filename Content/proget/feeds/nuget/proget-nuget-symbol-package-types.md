---
title: "Symbol Package Formats"
order: 2
---

When setting up a dedicated symbols feed on ProGet, the question often arises as to whether you should use the "standard" or the "legacy" symbol format. This article explains the difference between these two formats and how they relate to ProGet.

## What is a Symbol Package?
A symbol package is really just a NuGet package that has different contents and a  `.symbols.nupkg` or `.snupkg` file extension. Although this seems simple at first glance, it actually makes things quite complicated and causes some headaches.

### NuGet & Symbol Package Conflicts

NuGet packages are uniquely identified by their ID (e.g. `FooBar.Utils`) and Version (e.g. `3.4.1`), which are contained in the package's `.nuspec` manifest file.

The filename of the package doesn't affect this identification. In fact, the NuGet client doesn't transmit filenames to a feed when it publishes packages. This isn't necessary, because packages are uniquely identified by ID and Version.

What this means is that there's no way to distinguish between the `FooBar.Utils-3.4.1` and `FooBar.Utils-3.4.1` NuGet package. There's no way to tell which `FooBar.Utils-3.4.1` is a "library" package, and which `FooBar.Utils-3.4.1` is a "symbols" package.

This is why you need two feeds when using symbol packages, and also why we generally recommend using the simple, single-feed approach outlined in [ProGet as your symbol server](/docs/proget/feeds/nuget/symbol-and-source-server).

## Standard Format (.snupkg)
`.snupkg` packages are the ["new" format](https://docs.microsoft.com/en-us/nuget/create-packages/symbol-packages-snupkg){target="_blank"}, and can be created using by specifying the `SymbolPackageFormat`  on either the `nuget.exe` CLI or the `dotnet` CLI.

```
nuget pack MyPackage.nuspec -Symbols -SymbolPackageFormat snupkg
nuget pack MyPackage.csproj -Symbols -SymbolPackageFormat snupkg

dotnet pack MyPackage.csproj -p:IncludeSymbols=true -p:SymbolPackageFormat=snupkg
```
When created in this manner, a `.snupkg` file will just like the 
the  `.nupukg` file, but there will be `.pdb` files*instead of* the `.dll` files and the `.nuspec` manifest file will be slightly different.

This format is supported in ProGet as of v2022.20.

## Legacy Format
A [legacy symbol package](https://docs.microsoft.com/en-us/nuget/create-packages/symbol-packages) is just a NuGet package (.nupkg) with `.pdb` files *in addition to* the `.dll` files and everything else that's normally in the package. The easiest way to build this type of package is to simply tell NuGet to include `.pdb` files in the main package by adding `.pdb` to `AllowedOutputExtensionsInPackageBuildOutputFolder`:
```
nuget package MyPackage.csproj -p:AllowedOutputExtensionsInPackageBuildOutputFolder=$(AllowedOutputExtensionsInPackageBuildOutputFolder);.pdb
```
It is also possible to specify this value in the `.csproj` file instead:
```
<PropertyGroup>
    <AllowedOutputExtensionsInPackageBuildOutputFolder>$(AllowedOutputExtensionsInPackageBuildOutputFolder);.pdb</AllowedOutputExtensionsInPackageBuildOutputFolder>
</PropertyGroup>
```

## What about .symbols.nupkg Files?
`.symbols.nupkg` is a naming convention NuGet uses for generating legacy symbol packages (as described above) using the `-Symbols` argument to `nuget.exe`. When building a symbol package with this argument, NuGet will create two packages: the standard NuGet package without symbol files, and a legacy symbol package with the `.symbols.nupkg` suffix.

**This type of symbol package is identical to the legacy symbol package described above**, so if you build your symbol package this way, make sure to upload **only** the `.symbols.nupkg` file, since ProGet has no way to identify this type of package, uploading both will cause it to either overwrite the first one or give an error if overwrites are not allowed in the feed.

If you need to use legacy symbol packages, we recommend just building them as described in the previous section to avoid confusion.


## Which Format to Choose for ProGet?
Whenever possible, we recommend using the standard (`.snupkg`) format, since this is Microsoft's current recommendation, and the old format is considered legacy. However, ProGet still supports the legacy format, and the newer format only supports portable `.pdb` files, while the legacy format also supports Microsoft `.pdb` files.

If you use a dedicated feed for your symbol server, it doesn't really matter for ProGet. To ProGet, they both "look" the same because they're both NuGet packages.

However, because the old format is considered legacy, you should probably use the new format. In addition, the  `.snupkg` files are slightly smaller (they don't contain `.dll` files), and who doesn't like to save a bit of disk space from time to time.