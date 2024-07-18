---
title: "Helm (Kubernetes) フィードのホスト"
order: 7
---

Helmは、 [Kubernetes](https://kubernetes.io/) アプリケーションのパッケージマネージャーです。（参照：[Helm The package manager for Kubernetes](https://helm.sh/)、[Kubernetes (K8s)](https://kubernetes.io/)）

Helm フィード用の ProGet パッケージは[チャート](https://helm.sh/docs/developing_charts/#charts)として知られており、最も複雑なアプリケーションであっても記述することができ、Kubernetes デプロイのための再現性の高い、バージョン管理されたアプローチを提供することができます。

このフィードタイプは、ProGet 5.2 以降から利用可能です。

## **構成の前提条件**

Helmを利用してProGetフィードからパッケージをインストールするには、次のコマンドを実行して、ProGet フィードをリポジトリのローカルリストに追加してください。

```powershell
helm repo add proget http://{proget-server}/helm/{feed-name}
```

ProGet に多数のフィード構成が行われている場合を除いては、repo 名には proget を使用することをおすすめしています。多数のフィードが構成してある場合には、 proget-{feed-name} を使用してください。

## **共通タスク**

### **Helm チャートのインストール**

ProGet がホストするチャートをインストールするには、次のコマンドを実行します。

```powershell
helm repo update
helm install proget/{chart-name}
```

注意：Helm CLI は、カスタムリポジトリからインストールする引数として--repo を参照します。ProGet は--repo 引数と互換性がないため、次のエラーメッセージが表示される可能性があります。

"Error:Could not find protocol handler for: "

ProGetは、 helm repo add コマンドを使用してリポジトリを追加した場合にのみ機能します。

### **Helmチャートの作成**

基本的なHelmチャートは、次のコマンドを使用して作成できます。

```powershell
helm create {chart-name}
helm package {chart-name}
```

**create**コマンドは、必要に応じて変更できるチャートのテンプレート/スケルトンを生成します。**package** コマンドでは、ProGet にプッシュできるチャートを**.tgz**ファイルにパッケージジングします。

詳細については、Helmチャート作成に関するドキュメントをご参照ください。（参照：[Introduction to Helm](https://helm.sh/docs/intro/)）

## **チャートの公開**

Helm の実行可能ファイルでは、Helm チャートのプッシュをサポートしていないため、ProGet にはチャートを ProGet の Helm フィードに追加するために、3 つの方法があります。

## ProGet Web アプリケーションからアップロード

フィード概要のページで、[Add Package (パッケージの追加)] と [Upload Chart (チャートのアップロード)] を選択して、パッケージ化されたHelmチャート（つまり、 helm packageコマンドで生成した.tgzファイル）をアップロードします。

## HTTP 経由で公開する

HTTP 経由でHelmチャートをpush (プッシュ) するには、パッケージファイルをコンテンツとして PUT または POST リクエストを発行します。

http://{proget-server}/helm/{feed-name}を実行します。

次の実行例で完了します。

### **PowerShell**

```powershell
Invoke-WebRequest http://{proget-server}/helm/{feed-name} -Headers @{"AUTHORIZATION"="Basic " + [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("{username}:{password}"))} -Method PUT -InFile {chart-name}-{chart-version}.tgz
```

### **cURL**

```powershell
curl http://{proget-server}/helm/{feed-name} --user <user>:<password> --upload-file <chart-name>-<chart-version>.tgz
```

外部リポジトリからのPull (プル)

インストールしようとするチャートのバージョンが、外部 Helm リポジトリで利用可能な場合は、このオプションを使用します。

## **技術的な制限**

署名された（つまり、provenance） チャートは、現時点ではサポートしていませんが、将来の v5.2 メンテナンスリリースで利用できるようになる可能性があります。

## **コンテナの可視性**

Helm チャートは、いくつかのコンテナイメージを参照しています。これらのコンテナイメージは、 deployments.yaml のテンプレートファイルにデータを当てはめるときに使用する values.yaml ファイルで定義されています。１つのチャートが、他の多くの Helm チャートにも依存している場合もあります。これらの依存関係は、 values.yaml ファイルと deploment.yaml テンプレートにもあります。

ProGet は、チャートの values.yaml と依存関係にある values.yaml ファイルを解析して、使用しているコンテナイメージを見つけ出します。これらのコンテナイメージは、**Associated Container Images**（関連するコンテナイメージセクション） の下のチャート説明に表示されます。

ProGetは、関連付けられているコンテナイメージを ProGet のコンテナレジストリ内にあるコンテナイメージと照合しようと試みます。一致するものが見つかった場合、ProGet は Helm チャートを関連する ProGet のコンテナイメージと自動的にリンクします。

この機能は ProGet5.3 以降でご利用可能です。

## パーシング（**解析）の要件**

values.yaml でコンテナイメージを設定する場合、image プロパティには、二つの条件が必要です。

1. image プロパティが imageで終わる（例：image:、redisImage:、postgresImage:など）
2. 次のサブプロパティを含む：リポジトリ、タグ（またはdockerTag）、レジストリ（場合による）。

tagやdockerTagが省略された場合は、関連するコンテナイメージを定義する際に最新のタグが使用されます。

### **コンテナの脆弱性**

ProGet が脆弱性ソースを採用するように、コンテナレジストリが構成してある場合には、脆弱なコンテナイメージにリンクする Helmチャートが利用できます。(参照：[Vulnerability Scanning and Blocking](/docs/proget/sca/vulnerabilities))

これにより、レジストリからコンテナイメージを pull（プル）しようとしたときに、Kubernetes でのインストールに失敗する可能性があります。

脆弱性が評価されており [Blocked (ブロック済み)] とマークされている場合、または [Administration (管理)] > [Advanced Settings (詳細設定)]  で未評価の脆弱性をブロックできるようになっている場合には、コンテナイメージをブロックします。