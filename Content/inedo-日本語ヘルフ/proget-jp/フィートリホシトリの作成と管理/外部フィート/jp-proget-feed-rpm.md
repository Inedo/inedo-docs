---
title: "RPMフィードのホスト"
order: 6
hidden: true
---

RPM Package Manager （[RPM](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/7/html-single/rpm_packaging_guide/index)、以前の名称Red Hat Package Manager）は、RedHat や CentOS を含む多くの Linux ディストリビューションで使用されている、無料のオープンソースパッケージ管理システムです。

ProGet v5.2以降では、ProGet サーバに RPM フィードをホストして、RPM のパッケージをインストール、アップデート、アンインストールできます。

ちなみに、RPM がパッケージで、YUM は RPM を管理するパッケージマネジャーです。

## フィードを設定する前にすること

### **クライアントを構成する**

ProGet から RPM パッケージをインストールするには、各クライアントで次の手順を実行してください。

### **手順１：.repo ファイルを/ etc/yum.repos.d に追加する**

Yumは、リポジトリ構成を **/etc/yum.repos.d/** にある **.repo** ファイルに保管します。ProGet フィードをリポジトリとして登録するには、そのディレクトリに次のコンテンツを含む新しい**<RepoName>.repo** ファイルを作成します。

```powershell
[<RepoName>]
name=<RepoName>
baseurl=http://<ProGet>/rpm/<FeedName>/
gpgcheck=0
enabled=1
```

**<RepoName>**は何でもかまいませんが、わかりやすくするために ProGet のフィード名を使用することをおすすめします。

### **手順２：yum の構成を確認する**

yum がパッケージメタデータをダウンロードできることを確認するには、次のコマンドを実行します。

```powershell
yum repolist all
```

構成が正しい場合、（手順1で登録された ProGet フィードを含む） すべてのリポジトリが一覧表示されます。

## **共通タスク**

### **パッケージのインストール**

RPM パッケージは yum を使用してインストールします。ProGet をホストとして、パッケージをインストールするには次のコマンドを使用します。

```powershell
yum install <package-name>
```

### **パッケージの作成**

RPMパッケージの作成方法については、Red Hat デベロッパーブログで「[RPM packaging: A simplified guide to creating your first RPM](https://developers.redhat.com/blog/2019/03/18/rpm-packaging-guide-creating-rpm/)」をご覧ください。

### **パッケージの公開**

**yum** は、パッケージのアップロードをサポートしていないため、ProGet では RPM パッケージを公開するためにいくつかの方法をご提供します。

### **ProGetのWebアプリケーションからアップロードする方法**

フィードの概要ページで、[Add Package (パッケージの追加)] と [Upload Package (パッケージのアップロード)] を選択して、ブラウザーから直接.rpmファイルをアップロードします。

### **HTTP経由で公開する方法**

HTTP経由でRPMパッケージを push(プッシュ) するには、パッケージファイルをコンテンツとしてPUTまたはPOSTリクエストを、次のアドレスに発行します。 

http://{proget-server}/rpm/{feed-name}/

**リクエスト例：**

**cURL:**

```powershell
curl http://{proget-server}/rpm/{feed-name}/ --user <user>:<password> --upload-file {my-package}.rpm
```

**wget：**

```powershell
wget http://{proget-server}/rpm/{feed-name}/ --http-user <user> --http-password <password> --method POST --body-file {my-package}.rpm
```

**PowerShell:**

```powershell
Invoke-WebRequest http://{proget-server}/rpm/{feed-name}/ -Credential [System.Net.NetworkCredential]::new('<user>', '<password>') -Method PUT -InFile {my-package}.rpm
```

### 技術的な制限

- コネクタはサポートしておりません。
- GPG 署名については現在サポートしていませんが、開発予定です。
- ProGet の YUM・RPM フィードの新しい機能を誰よりも早く試したい方は、ぜひお気軽にお問合せください。