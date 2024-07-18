---
title: "Inedo.AssetDirectories Library"
order: 3
---

The Inedo.AssetDirectories library is available as a simpler alternative to the Asset Directory API for .NET projects.

### Example: Downloading a File
```C#
var client = new AssetDirectoryClient(endpointUrl: "https://my.proget/endpoints/<AssetDirectoryName>", apiKey: "<API KEY>");

// Download the archive.zip file to C:\temp\archive.zip.
using var stream = await client.DownloadFileAsync("archive.zip");
using var dest = File.Create(@"C:\temp\archive.zip");
await stream.CopyToAsync(dest);
```

### More Information
See the [Inedo.AssetDirectories](https://github.com/Inedo/Inedo.AssetDirectories) project page on GitHub for more examples. Reference the Inedo.AssetDirectories NuGet package to use the library in your project.