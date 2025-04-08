---
title: "npm (Node.js)フィードのホスト"
order: 8
hidden: true
---

npm の正式名称は、Node Package Manager です。npm の正式名称からも解るように、Node.jsのパッケージ（Package ）を管理する（Manager）ツールです。

ProGet では、ProGetをインストールしたサーバに npm フィードをホストして、npm のパッケージをインストール、アップデート、アンインストールできます。ProGet npm フィードは、スコープ付きとスコープなしのどちらの npm パッケージもサポートしています。

スコープ (Scope) とは、パッケージ名の前につける、@から/までの名前空間のことです。@以降は、npm ユーザーアカウントまたは組織名が入ります。

## **npmフィードの技術的な制限**

### Windows 統合認証に非対応

npm クライアントは Windows 統合認証の使用をサポートしていないため、ProGet は Basic HTTP 認証を優先して npmAPI エンドポイントの使用を無効にしようとします。ただし、URLごとにWindows認証を無効にすることが、すべての環境で機能するとは限りません。問題が生じたら、別のライセンスを取得せずに、Windows 認証を無効にして ProGet 用に IIS で新たなにWebサイトを作成してください。

### **ビルドメタデータと npmjs.org**

[npm セマンティックバージョニングのドキュメント](https://docs.npmjs.com/about-semantic-versioning)（リンク先英語）には SemVer2 のサポートが記載されていますが、ビルドメタデータ（つまり、「+」記号が含まれるバージョン）にはいくつかの矛盾があります。このビルドメタデータは、多くの [npmjs.org](http://npmjs.org/) クエリでは公開されていませんが、ProGet クエリでは公開されています。[npmjs.org](http://npmjs.org/) へのコネクタを使用すると、パッケージが見つからないなど、npm クライアントで予期しない動作が発生する可能性があります。

このエラーは、@hot-loader/react-domなど、ごく少数のパッケージでのみ確認されています。問題が継続する場合、動作が変更される可能性があります。（参照：[NPM Connector returns plus "+" in versions](https://forums.inedo.com/topic/2948/npm-connector-returns-plus-in-versions)）

## **ProGet で npm フィードを作成する方法**

ナビゲーションバーの [Feeds (フィード)] タブをクリックすると、システム内のフィードリストが表示されます。

[Create New Feed (新しいフィードの作成) ] ボタンをクリックして、[npm] を選択します。

[**Feed name (フィード名)**] のフィールドに値を入力します。この値は npm の個人レジストリの名称になるので、URLに簡単に入力できる文字列にしておくことをおすすめします。たとえば、 **private-npm** などが使用できます。

[**Create Feed (フィード作成)**] ボタンをクリックすると、新しく作成されたレジストリに自動的に移動します。

ProGet サーバがインターネットに接続している場合は、npmjs.org にパッケージリストが表示されます。これらは*コネクタパッケージ*と呼ばれ、ProGet インスタンスのホストとはなり得ませんが、別のサーバーからは透過型プロキシとなっています。

透過型プロキシとは、ブラウザにプロキシの設定をしていない状態でもプロキシサーバ経由による Web アクセスをさせる方法です。

npm フィードを作成した後は、フィード内に直接、またはページ上部の [**Connectors(コネクタ)**] タブをクリックして、 *npmjs.org* のコネクタを追加します。

このように、パブリックパッケージをプライベートパッケージに加えることで豊富なパッケージ ライブラリにアクセスできるようになります。外部パッケージから一部のみを追加したい場合には、コネクタでのフィルタリングも可能です。ただし、デフォルトではフィルタリングは実行されません。コネクタの詳細については、**コネクタのドキュメント**をご参照ください。

## **npm の設定**

まず、npmに レジストリで探す場所を指示しておく必要があります。デフォルトでは、 *npmjs.org*用に環境設定されています。次のコマンドを使用して、ProGet サーバーと上記の手順で作成したNPM フィードの名称にした **プライベートnpm** の名前/ポートをProGetに置き換えます。

```powershell
[~]$ npm config set registry http://proget/npm/private-npm
```

### **パッケージのインストール**

npm パッケージは、[npm](https://docs.npmjs.com/cli/install) を使用してインストールします。（参照：[npm-install](https://docs.npmjs.com/cli/v6/commands/npm-install)）ProGetフィードからパッケージをインストールするには、最初に上記のコマンドを使用してnpmを設定してから、次のコマンドを使用してください。

```powershell
npm install {package-name}@{package-version}
```

## **npmのトークン認証**

npm adduser コマンドを使用せずにプライベートレジストリを操作するには、次の手順に従ってください。

###  **APIキーの作成**

[Feed Access (フィードアクセス)] を使用して、（ [Admin (管理)] > [API Key & Access Logs (APIキーとアクセスログ)] ）で ProGet の API キーを作成します。このキーを、特定のアクセス許可をしたユーザに関連付けることで、さらに制限できます。 （参照：[API Access and API Keys](/docs/proget/api/apikeys)）

### **APIキーの暗号化**

キーを作成したら、ユーザー名の api とともにキーを暗号化する必要があります。エンコードは base64 形式で実行します。エンコードする前の文字列は次のようになります。 api：{APIKEY}

このトークンを取得したら、npm を使用して_auth 値を設定してください。また、always-auth は、true に設定することをお勧めします。

```powershell
[~]$ npm config set always-auth=true
[~]$ npm config set _auth={ENCODEDAPIKEY}
```

### **npmでAPIトークンを作成する方法**

まず、[ProGet] >  [Admin (管理)] >  [API Keys (APIキー)] に移動し、キーの値 **apikey12345** を入力してキーを作成します。Feed APIにチェックが入っていることを確認してください。

次に、base64 エンコーダーを見つけます。 Googleで検索して、エンコードを実行できるWebサイトを見つけるか、ブラウザーの開発コンソールで btoa() 関数をご活用ください。エンコードする値は **api:apikey12345** で、出力は**YXBpOmFwaWtleTEyMzQ1** です。

そして、次のNPMコマンドを実行できます。

1. npm config set always-auth=true を実行します

1. 2.npm config set _auth=YXBpOmFwaWtleTEyMzQ1を実行します。

その後、作成が完了するはずです。**apikey12345** の部分は、説明のための単なるサンプル値です。入力する値は何でもかまいませんが、ドキュメントで使用する値は避けてください。

## **パッケージの公開**

パッケージは、npm を使用して ProGet に公開することも可能です。まず、 **adduser**コマンドを使用して ProGet のデフォルトの管理者アカウントでログインをします。パスワードの入力を求められたら、ProGet へのログインに使用したパスワードと同じものを使用します（デフォルトでは管理者です）。

```powershell
[~]$ npm adduser
[~]$ npm publish package.tgz
```