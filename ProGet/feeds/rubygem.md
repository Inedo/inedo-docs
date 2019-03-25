---
title:
title: Ruby Gems
subtitle: RubyGem
sequence: 150
keywords: proget,feeds,ruby
---
A Ruby Gems feed in ProGet acts as a private Ruby Gems repository. Gems can be used directly by the gem client, and also browsed directly on the ProGet site.

### Installing Packages
Ruby Gems packages are installed using [gem](https://guides.rubygems.org/command-reference/#gem-install). To install a package from a ProGet feed, use the following commands:

##### 1. Configure gem / Add Source
```
gem sources --add http://{proget-server}/nuget/{feed-name}/
```

##### 2. Install Module
```
gem install {package-name} -v {package-version}
```
