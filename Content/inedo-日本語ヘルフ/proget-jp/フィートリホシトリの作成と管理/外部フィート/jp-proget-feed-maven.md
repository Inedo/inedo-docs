---
title: "Mavenフィードのホスト"
order: 12
hidden: true
---

ProGet では、ProGetをインストールしたサーバに Maven フィードをホストして、Maven のパッケージをインストール、アップデート、アンインストールできます。

Maven フィードには、maven2 と互換性のある Maven アーティファクト リポジトリが含まれています。Maven の動作は、他のフィードタイプとは少し異なります。

## **Maven とは**

Maven アーティファクトは自己完結型のアーカイブではありません。どちらかと言うとアーティファクトは、関連メタデータを含むグループ化ファイルです。この違いのため、Mavenフィードはドロップしたパスをインポートするといった動作はサポートしていません。

Maven のコネクタも、一般的なフィードタイプとは少し異なっています。リモート Maven リポジトリでの検索または一覧表示には国際的な標準が存在しないため、ProGet は*すべての*アーティファクトのインデックスを提供できる、かなり古い API を活用しています。

ProGetサービスのタスクは、そのインデックスを定期的にダウンロードしてローカルのLuceneデータベースに解凍し、それを使用してリモートリポジトリを検索します。

パブリックApache Mavenリポジトリは、非常にリソースを消費する操作になる可能性があります。この検索機能が必要ない場合は、ProGet でこのタスクを無効にすることをお勧めします。名前で直接アクセスできるアーティファクトには、このインデックスは必要ありません。

ProGet 5.1.0以降、Mavenフィードは代替パッケージストア（AWS、Azure）をサポートしています。

### **リポジトリの追加**

ProGet の Maven フィードを Maven リポジトリとして追加する方法は2つあります。最初の方法は、POM xml でフィードを指定することです。*project > respoitories* 間のエレメントに、ProGetフィードを識別するエレメントを *repository* で追加します。

例えば：

```powershell
<project>
...
    <repositories>
        <repository>
            <id>ProGet</id>
            <name>{Maven-Feed-Name}</name>
            <url>{Maven-API-endpoint-url}</url>
        </repository>
    </repositories>
...
</project>
```

フィードを指定するもう1つの方法は、 {USER_HOME}\.m2\settings.xm ファイルにプロファイルを作成することです。プロファイルの例は次のとおりです。

```powershell
<settings>
    ...
    <profiles>
    ...
    <profile>
        <id>ProGetProfile</id>
        <repositories>
            <repository>
                <id>ProGet</id>
                <name>{Maven-Feed-Name}</name>
                <url>{Maven-API-endpoint-url}</url>
            </repository>
        </repositories>
    </profile>
    ...
    </profiles>
     
    <activeProfiles>
        <activeProfile>myprofile</activeProfile>
    </activeProfiles>
    ...
</settings>
```

Mavenリポジトリの追加に関する詳細は、Maven ガイドの（複数リポジトリのセットアップ）をご参照ください。（参照：[Setting up Multiple Repositories](https://maven.apache.org/guides/mini/guide-multiple-repositories.html)）

### **Maven フィードの認証**

Maven クライアントの認証設定は、 settings.xlm ファイルにあります。（参照：[Settings Reference](https://maven.apache.org/settings.html)）

スタンダードな<username>エレメントと<password>エレメントを使用して、ProGetのユーザー名/パスワードを保存するか、またはユーザー名としてapiを指定し、パスワードとしてAPIキーを指定することもできます。（参照：[API Access and API Keys](/docs/proget/api/apikeys)）