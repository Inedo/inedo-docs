---
title: "RubyGems (ruby)"
order: 6
---

A Ruby Gems feed in ProGet acts as a private Ruby Gems repository. Gems can be used directly from the Gem client and can also be viewed directly on the ProGet website.

## Prerequisite Configuration

Although ProGet was tested with older versions, we recommend using rubygems 3.0.0 or higher. You can use `gem --version` to see which version of rubygems you have installed.

### Gem Client Configuration

Before installing packages from ProGet, you should add ProGet to your `gem` installation by using the [`gem sources` command](https://guides.rubygems.org/command-reference/#gem-sources).

```(Shell)
gem sources --add http://«proget-server»/rubygems/«feed-name»
```

If you want to install packages only from ProGet, you'll want to remove other sources. 

```(Shell)
$ gem sources
*** CURRENT SOURCES ***

https://rubygems.org
http://«proget-server»/rubygems/«feed-name»

$ gem sources --remove https://rubygems.org/
https://rubygems.org/ removed from sources
```

Note that these sources are stored in your `~/.gemrc` file.

:::(Info) (Alternative Configuration Methods)
Instead of adding ProGet to your sources file, you can set the `RUBYGEMS_HOST` environment variable.
```(Shell)
$ export RUBYGEMS_HOST=http://«proget-server»/rubygems/«feed-name»
```
You can also always specify the `-source` argument on `gem` commands.
:::

### Testing Client Configuration

You can use the [`gem search` command](https://guides.rubygems.org/command-reference/#gem-search) to make sure that your client is properly connected to ProGet.

```(Shell)
$ gem search «search-string»
```

This will search all sources configured in your `~/.gemrc` file to be searched. Use the `--source` argument to specify your ProGet server if you have multiple sources configured.


### Authentication

The first time `gem` needs to authenticate to ProGet (whether for installing or publish packages), it will prompt for credentials and then store the results in your `~/.gem/credentials` file. You can enter the same username/ password you used to authenticate to ProGet, or a username of `api` and a valid API Key.


## Installing Packages

You can use gems from ProGet just like you would use gems from `rubygems.org`. Gems are generally installed using the [`gem install` command](https://guides.rubygems.org/command-reference/#gem-install):


```(Shell)
$ gem install «package-name» --version «package-version»
```

## Creating Packages

There are no special considerations when creating gems for use in ProGet. You can simply use the [`gem build` command](https://guides.rubygems.org/command-reference/#gem-build). 


```(Shell)
$ gem build «package-name».gemspec
  ```

See "[Make your own gem](http://guides.rubygems.org/make-your-own-gem/)" in the RubyGems documentation to learn more


## Publishing Packages

To push packages to ProGet, you can use [`gem push` command](https://guides.rubygems.org/command-reference/#gem-push):

```(Shell)
$ gem push --host http://«proget-server»/rubygems/«feed-name» «package-name-version».gem
```


## Troubleshooting: Bundler & Dependencies

When using a ProGet feed that's connected to rubygems.org, you may notice that running `bundle update` on a gem like [fastlane](https://rubygems.org/gems/fastlane) takes a *preposterous* amount of time and resources. This is because `fastlane` has hundreds of dependencies, and `bundler` needs to download and inspect every single version of every single gem to find if it's a suitable match.

This is ultimately a result of the [dependency API being disabled on RubyGems.org](https://blog.rubygems.org/2023/02/22/dependency-api-deprecation.html) and disabled on ProGet by default.

:::(Info) (TIP: Enable the Dependency API on your Feed)
Enabling the Dependency API will (under Manage Feed) will resolve the issue of "massive gems", but it will be slower for smaller gems. 
:::

The dependency API was the primary way that Bundler fetched dependency info for Gemfile resolution. However, this caused significant load on the RubyGems.org servers, so a [new API was developed](https://andre.arko.net/2014/03/28/the-new-rubygems-index-format/) and Bundler started using that if available.

Unfortunately the new API (called the Compact Index API) was only designed with RubyGems.org in mind, and it doesn't work very well with private repositories like ProGet.


### Support for the Compact Index API

The [Compact Index API](https://github.com/rubygems/compact_index) has three endpoints, two of which are supported by ProGet.

<table>
    <tr><td><code>/names</code></td><td> alphabetically-sorted list of gem names</td><td>✅ Supported</td></tr>
    <tr><td><code>/info/gem_name</code></td><td>precedence-sorted list of versions for a gem</td><td>✅ Supported</td></tr>
    <tr><td><code>/versions</code></td><td>tail-appended list of gems published to the library</td><td>🟡 Partially Supported</td></tr>
</table>

The reason that `/versions` is not fully supported is because it's mostly incompatible with the notion of connectors, private packages, etc. It's basically a massive plaintext file (28mb+ and growing every day) that's kind of like the old "dependencies" endpoint. 

Rubygems.org appends new lines to the file (which they store on cloud storage), and these lines may be duplicative of previous data in the file. The Bundler client uses ETags and HTTP Range requests to do "incremental" updates to the file, and then reads it "backwards". 

For users, this means that the first time you run `bundler`, it will downloads the whole file... and then it only downloads new lines each time. This approach works because RubyGems.org is essentially read-only, and packages cannot be deleted or replaced.

A private repository like ProGet can't really maintain a static text file because we have to aggregate connector data with local packages. Those are both frequently changing/dynamic datasets, and can't be tail-appended static file. And there's also load-balanced servers to consider.

Although ProGet can easily generate this text file on demand,  `bundler` would need to frequently download the whole file. For most use cases, that's slower that downloading (and caching) all of the gems.

Instead, when the Dependencies API is enabled, ProGet will download the `/versions` file from RubyGems.org, process it, and use that to return Dependencies .
