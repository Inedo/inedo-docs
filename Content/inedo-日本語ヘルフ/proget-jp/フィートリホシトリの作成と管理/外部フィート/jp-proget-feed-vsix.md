---
title: ".vsix フィードのホスト"
order: 3
hidden: true
---

Visual Studio Code (VSCode) では、Marketplace に多くの拡張機能が公開されています。

しかし、社内用の非公開な拡張機能を開発したいときや、公開前にチームメンバーと共有してテストしたいこともあるでしょう。

その場合、*.vsix のパッケージファイルにすれば、Marketplace に公開することなく、拡張機能のインストールができるようになります。

ProGet は、社内用の *.vsix 拡張機能パッケージの、プライベートリポジトリとして利用できます。

## ProGet で **Visual Studioの拡張機能（.vsix）を利用する方法**

ProGet の VSIX フィードには、Visual Studio の拡張機能が保存されており、Visual Studio からインストールしてご利用いただけます。

Visual Studio では、標準の拡張機能ギャラリーを無効にしたり削除したりすることはできません。このギャラリーは ProGet が公開している、ドキュメント化された Atom VSIX フィードとは、また別の内部 API を使用しています。そのため現在、ProGet では Visual Studioギャラリーへのコネクタは作成できません。ただし、そのほかのAtom VSIXフィードへのコネクタはサポートしています。

パッケージを追加するための VSIX によって定義された公式の API はありません。ツールやスクリプトから .vsix の拡張子をアップロードしたい場合は、フィード API のエンドポイント URL に拡張子を PUT または POST するだけです。

```powershell
# PowerShell example
Invoke-RestMethod https://proget.example.com/vsix/FeedName `
-InFile .\MyExtension.vsix `
-Headers @{"Authorization" = "Basic " + [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes("api:xxxxxxxxxxxxxx"))}
```

PowerShell は、デフォルトではTLS 1.2（ RFC 5246 、2008年8月に定義）をサポートしていません。（参照：[The Transport Layer Security (TLS) Protocol Version 1.2](https://tools.ietf.org/html/rfc5246)）

2018年6月以降の PCI コンプライアンス要件などにより、サーバーが TLS1.0 接続を許可していない場合、、上記のコマンドを実行する前に TLS1.2 を有効にしてください。

TLS 1.2 を有効にする方法については、 KB#1161 をご参照ください。（参照：[TLS v1.2 Configuration and Connection Errors KB#1161](https://inedo.com/support/kb/1161/tls-v12-configuration-and-connection-errors)）

## Visual Studio にフィードを追加するには

VSIXフィードを Visual Studio に追加するには、拡張機能ギャラリーを導入してください。

操作手順は次のとおりです。

[Tools (ツール)] > [Options (オプション)] > [Environment (環境)] > [Extensions(拡張機能)] へと移動します。

[Additional Extension Galleries(追加の拡張機能ギャラリー)] **の下の [*Add(追加)*] ボタンをクリックします。

名前を入力し、URLをVSIXフィードのAPI エンドポイント URLで設定します。

これにより、[Manage Extensions(拡張機能の管理)] ウィンドウからは、標準の拡張機能に加えて、VSIXフィードの拡張機能もインストールできます。

## VSIXでの制限について

### **統合Windows認証**

Visual Studioは、ProGetインスタンスへ「匿名」でアクセスする必要があります。ProGetのインスタンスで統合認証が有効になっている場合には、匿名でアクセスすることはできません。

これを回避するには、ディスク上で同一パスを要求してくる統合Windows認証は有効にせずに、IISで2つ目のサイトをセットアップしてご利用ください。