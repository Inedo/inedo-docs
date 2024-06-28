---
title: "RubyGemフィードのホスト"
order: 11
---

# ProGet で RubyGems フィードをホストする方法

RubyGems は、Ruby用のパッケージ管理システムです。

Rubyでは、パッケージをGemと呼び、それを管理するシステムを RubyGems と呼びます。一部、Gem をパッケージ管理ツールと呼ぶウェブサイトもあります。

また、Ruby には、Bundler と呼ばれる、gemをどのバージョンで使うか一元管理できるgemもありますが、ProGet では Bundler も使えます。

**パッケージのインストール**

Ruby Gemsパッケージは、 [gem](https://guides.rubygems.org/command-reference/#gem-install) を使ってインストールします。ProGetのフィードからパッケージをインストールするには、次のコマンドを使用してください。

**1.gemの構成/ソースの追加**

```powershell
gem sources --add http://{proget-server}/nuget/{feed-name}/
```

**2.モジュールのインストール**

```powershell
gem install {package-name} -v {package-version}
```