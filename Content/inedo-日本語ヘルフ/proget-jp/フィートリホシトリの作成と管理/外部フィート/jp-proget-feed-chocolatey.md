---
title: "Chocolatey フィードのホスト"
order: 9
hidden: true
---

[Chocolatey](https://chocolatey.org/) は、Windows 用のパッケージマネージャーです。apt や yum のような Linux ツールに似ていますが、Windows の特性を考慮した、使いやすい機能が盛り込まれています。必要なアプリケーションやツールを素早くインストールする分散型フレームワーク、Chocolatey の導入方法をご覧ください。

## **パッケージのインストール**

Chocolateyパッケージは、[Chocolatey](https://chocolatey.org/docs/commands-install) サイトの手順にしたがい、コマンドプロンプトを用いてインストールします。ProGet のフィードからパッケージをインストールするには、次のコマンドを使用します。

```powershell
choco install {package-name} --version {package-version} --source http://{proget-server}/nuget/{feed-name}/
```

## **パッケージの作成**

ProGet をホストとしたパッケージの作成方法については、[「Creating Chocolatey Packages](https://chocolatey.org/docs/create-packages)」のページを参照してください。

##  **NuGet フィードとの違い**

Chocolatey は NuGet インフラストラクチャ上に構築されています。そのためProGet の Chocolatey フィードも、実は [NuGet](/docs/proget/feeds/nuget) フィードの一種です。機能的には NuGet フィードと、まったく同じように動作しますが、ユーザーインターフェイスは Chocolatey パッケージに合わせて次のように最適化されています。

- Chocolateyフィードのパッケージには、Chocolateyで使用するのとは異なるアイコンが表示されています。
- パッケージのインストール手順では、NuGetの代わりに「**choco**」を使用したパッケージインストール方法も紹介されています

Chocolatey の創設者ロブ・レイノルズが、ProGet と Chocolatey のデモを公開しているビデオがご覧いただけます。Windows パッケージ管理へのソリューションを提供するために、ProGet と Chocolatey をどう融合させていくべきかを再確認してください。

![Video](https://www.youtube.com/watch?v=BcTYGf7sQ8Q&feature=emb_title){height="480" width="640"}