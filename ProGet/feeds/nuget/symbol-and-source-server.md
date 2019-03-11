---
title: Symbol and Source Server
sequence: 30
keywords: proget, feeds, packages, symbols, source, nuget, visual-studio-online
---


## Symbol and Source Server
A NuGet feed in ProGet may be configured as a Symbol/Source server compatible with debuggers such as Visual Studio and WinDbg. The following is supported by the ProGet symbol server:

{.docs}

- Microsoft PDB format (C/C++, .NET)
- Source server
- Portable PDB format (.NET)
- PDBs in connector packages
- Source server for [ProGet](/support/documentation/proget/installation/installation-guide/linux-docker)

### Accessing Symbols

When the Symbol Server is enabled, any symbols files stored in local Feed packages will be accessible via the symbol server URL:

:::attention {.best-practice}
```
{http://&laquo;proget-server&raquo;/symbols/&laquo;Feed&raquo}
```
:::

### Symbol Packages

ProGet supports using NuGet symbol packages built with the **Symbols** argument. These symbol packages are nothing more than a standard NuGet package with .pdb files and possibly source files included, and they have a *.symbols.nupkg* or *.snupkg* file extension by convention. NuGet produces two packages (one with symbols and one without) because one is intended to be pushed to NuGet.org, while the other is intended to be pushed to a symbol feed.

Because ProGet integrates symbols in all of its NuGet feeds, having two packages is not necessary and is actually counterproductive. Symbol packages only differ from "normal" packages by convention, so attempting to push both to a ProGet feed will result in a race condition - whichever package is pushed "last" will overwrite the original one.

To summarize, **push only the symbol package to your ProGet feed if you intend to use the symbol server.** To prevent symbols from being downloaded with the NuGet package, see the *Strip symbol files from packages downloaded from this feed* option on the Manage Feed page under *Symbol Server* settings.

## Debug Into Internal NuGet

To take full advantage of ProGet's symbol/source server, the published NuGet package must include symbol (pdb)
files for every debuggable assembly, and the source files must be included in the /src/ folder of the
package. See the <a href="http://docs.nuget.org/docs/creating-packages/creating-and-publishing-a-symbol-package">NuGet documentation</a>
on building a symbol package for detailed information.

:::attention {.technical}
ProGet will index and return portable PDB files as well as the traditional Microsoft PDB files, but portable PDB files are <em>not</em> transparently rewritten to enable them to work with source code in the embedded NuGet package. If you would like to debug into the source code associated with a portable PDB file, consider using a build tool such as [SourceLink](https://github.com/ctaggart/SourceLink) to embed source file URLs (or the files themselves) during compilation.

If you would like support for rewriting portable PDB files in the same way ProGet handles Microsoft PDB files, [let us know](/contact)
:::

#### Configuring ProGet

In order for ProGet to serve symbols for a particular NuGet feed, it must be configured to do so. Visit the Manage Feed page, and ensure that the options under Symbol Server are correctly checked, while noting the URL of the feed's symbol server to be used within Visual Studio:
![](/resources/documentation/proget/symbols/feed-configuration.png)

#### Verifying Indexed Symbols

Once a package with symbols is uploaded to the feed, browsing to the Package Details page will indicate whether symbols have been indexed and whether the package contains sources:
![](/resources/documentation/proget/symbols/package-details.png)

You can download the package and inspect it yourself to verify this using the <em>Download Package with Symbols</em> link (which appears if the feed is configured to strip symbols).

#### Configuring Visual Studio
##### Enable Symbol Server Support
In order to debug into NuGet package libraries, Visual Studio must be configured to use ProGet as a symbol server. Select **Debug < Options...** from the menu bar, then browse to **Debugging > Symbols** in the tree menu. Add the symbol server URL found on the Manage Feed page earlier, and specify a Symbol Cache Directory. By default Visual Studio will use ```%LOCALAPPDATA%\Temp\SymbolCache```, but you may specify any path.

![](/resources/documentation/proget/symbols/enable-symbol-server.png)

#### Enable Source Server Support

To configure source server support, browse to **Debugging > General** in the debugging options tree menu, and make sure the following settings are checked/unchecked as follows:

{.docs}
- **Enable Just My Code**
- **Enable source server support**
Additionally, you may have to uncheck:
- **Enable .NET Framework Source Stepping**


in some cases. The settings should look like the following:
![](/resources/documentation/proget/symbols/debug-settings.png)

### Testing the Configuration

A simple way to test out the configuration is to create a console application that consumes the NuGet package with symbols, write some throwaway code that you know will throw an exception, then click the Start button in Visual Studio to begin debugging:
![](/resources/documentation/proget/symbols/code1.png)

If everything is configured correctly, Visual Studio will attempt to load symbols locally, then query the ProGet symbol server if they cannot be found, and the exact line that throws the exception will become highlighted:
![](/resources/documentation/proget/symbols/code2.png)

#### Troubleshooting

In some cases, the symbol server will fail for any number of reasons, the most common being misconfiguration. To view the
PDBs that Visual Studio has loaded, select the  **Debug > Windows > Modules** menu option while debugging, and find the desired DLL in the list. The symbol status will appear, along with the version and path to both the DLL and the downloaded symbols.

![](/resources/documentation/proget/symbols/modules.png)

Whether the symbols loaded or not, you can right-click on the DLL and select "Symbol Load Information..." to view the diagnostic messages associated with the symbol server for that library.

![](/resources/documentation/proget/symbols/symbol-load-info.png)

The hex string in the file path should also start with the GUID listed in ProGet:

![](/resources/documentation/proget/symbols/hex.png)

![](/resources/documentation/proget/symbols/guid.png)

#### Common Errors

The most common errors (based on previous support inquiries) include:
{.docs}

- Using the wrong URL, e.g. (http://&laquo;proget-server&raquo;/) **nuget**<</feed-name/>> instead of the correct (http://&laquo;proget-server&raquo;)<strong>symbols</strong>feed-name
- Pushing both NuGet packages (with and without symbols)
- Trying to consume symbols for connector packages (which is not supported, workaround is to pull packages locally or use a separate feed for symbols)
- Not including source files under the /src/ directory at the root of the .nupkg file
