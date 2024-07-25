---
title: "PowerShellフィードのホスト"
order: 13
hidden: true
---

PowerShell フィードは、PowerShell モジュールを保管することに特化した [NuGet フィード](/docs/inedo-日本語ヘルフ/proget-jp/フィートリホシトリの作成と管理/外部フィート/jp-proget-feed-nuget){target="_blank"}です。

PowerShell フィードへは、[PowerShellGet](/docs/inedo-日本語ヘルフ/proget-jp/フィートリホシトリの作成と管理/外部フィート/jp-proget-feed-powershell){target="_blank"} からアクセスします。

## PowerShell **モジュールのインストール**

PowerShell パッケージは、 [PowerShell](https://docs.microsoft.com/en-us/powershell/module/PowerShellGet/Install-Module) からインストールします。

ProGetフィードからパッケージをインストールするには、次のコマンドを使用してください。

## **1.モジュールの登録**

```powershell
PS> Register-PSRepository -Name "{feed-name}" -SourceLocation "http://{proget-server}/nuget/{feed-name}/"
```

## **2.モジュールのインストール**

```powershell
PS> Install-Module -Name "{package-name}" -RequiredVersion "{package-version}" -Repository "{feed-name}"
```

## **NuGet フィードとの違い**

PowerShell モジュールのパッケージは、実際には NuGet パッケージです。そのため PowerShell フィードは、ユーザーインターフェイスで微調整を実行するいくつかの場合を除いて、機能的には NuGet フィードと同じように動作します。

- PowerShell フィードのパッケージには独自アイコンがあり、PowerShell モジュールであることが一目でわかる表示です。
- パッケージのインストール手順は、NuGet ではなくPsGetのインストール手順として表示されます。
- PowerShell パッケージではタグの使用方法が異なること、またタグの数が多く概要に表示することが現実的でないため、タグは [Browse Feed (フィード参照)] のページに表示されません。

## **PowerShell モジュールパッケージの Quirks バージョン**

Microsoft による PowerShell ギャラリーの検証バグのため、いくつかのパッケージには2つの異なるバージョン番号が振り分けてあります。1つは.nuspecファイルに、もう1つはモジュールの.psd1として確認できます。

2種のバージョン番号が存在することで、多くの問題が起こり得ます。最も一般的なのは、ProGet リポジトリからパッケージを Windows マシンにインストールしたときに、「間違った」場所に入ってしまうことです。

たとえば、SqlServerDscは

**C:\Program Files\WindowsPowerShell\Modules\SqlServerDsc\12.2.0**

にインストールされますが、.psd1で実際に表示される番号は、12.2.0.0です。その結果、PowerShell は、マニフェストが正しくないと判断して、モジュールの読み込みを拒否します。

回避策の1つは、PSGallery コネクタのキャッシュをオフにしてから、バージョンが正しく設定されなかったパッケージを確認して削除していくことです。（最初に ProGet にパッケージをプルしてから、削除してください。）すると、ProGet は正しいバージョン番号でコネクタから直接にパッケージの配布ができるようになります。

2019年4月8日の時点で、この方法は PowerShellGallery Issue＃55に掲載されており、回避策は ProGet の「Q＆Aサイト」で説明しています。（参照：[No validation of version number mismatches between .nuspec and .psd1 #55](https://github.com/PowerShell/PowerShellGallery/issues/55)、[Proget keeps mangling NuGet package version numbers!](https://forums.inedo.com/topic/2643/proget-keeps-mangling-nuget-package-version-numbers)）

## PowerShell のパッケージが見つからない場合

Microsoft による PowerShell ギャラリーはバグにより、API が一部のパッケージは存在していないものとして表示します。PowerShell クライアントは、別のクエリを使用して再度クエリを実行することで、このバグを回避しようとしますが、この回避策を試行するのは PowerShell ギャラリーにクエリを実行するときにのみです。

ProGet にクエリを実行する場合については、回避策は試行されません。

ProGet にクエリを実行する際に、バグを回避する最も簡単な方法は、パッケージをローカルにプルすることです。

2018年10月1日の時点で、この事象はPowerShellGallery Issue＃30に掲載されています。（参照：[IsAbsoluteLatestVersion is false for all versions in some packages #30](https://github.com/PowerShell/PowerShellGallery/issues/30)）

## **PowerShell フィードへの公開**

PowerShell 5.0 以降または PowerShellGet からは、ProGetのPowerShell フィードに登録することで、簡単に公開を実行することができます。

```powershell
Import-Module PowerShellGet

Register-PSRepository -Name MyProGetFeed -SourceLocation <ProGetUrl> /nuget/<FeedName>/ -PublishLocation <ProGetUrl>/nuget/<FeedName>/
Publish-Module -Path <ModuleFile> -NuGetApiKey <UserName>:<Password>-Repository MyProGetFeed
```

ユーザー/パスワード 情報の代わりにAPIキーを使用するには、ProGetでフィード APIキーを作成し、**Publish-Module** を、次のように入力してください。(参照：[API Access and API Keys](/docs/proget/reference-api/proget-apikeys))

```powershell
Publish-Module -Path <ModuleFile> -NuGetApiKey <API Key> -Repository MyProGetFeed
```