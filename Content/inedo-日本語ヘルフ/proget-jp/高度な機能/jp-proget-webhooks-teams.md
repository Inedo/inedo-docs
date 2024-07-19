---
title: "Webhook で ProGet とTeams を接続する方法"
order: 3
---

Webhook を使えば、2 つのツールを接続してユーザーに通知したり、 自動化されたワークフローを動作させたり、特定のイベントを 監査したりできます。ProGet の Webhook は高度なカスタマイズが可能です。まず、[Post (投稿)] や [Delete (削除)] など、7 種類の「メソッド」に加え、[Pulled (プル)] や [Promoted (プロモーション)] など、6 種類の「イベント」を使って 156 種類もの通知アラートが設定できます。

このページでは、ProGet を Teams に接続する方法と、サンプルのペイロードメッセージをコピーして自分の Webhook に貼り付ける方法を紹介します。

## Teams の Webhook 設定

Microsoft Teamsでは Webhook を簡単に作成できます。まず、Teams 内で目的のチャネルに移動し、3点リーダをクリックします。その後、[Connectors (コネクタ)] からIncoming Webhook の拡張機能を選びます。

<!--
![image](https://docs.inedo.com/resources/documentation/proget/TeamsConnectorAdd.png){height="" width=""}
 -->
 
Webhook に名前を付け、任意で画像を追加します。

<!--
![image](https://docs.inedo.com/resources/documentation/proget/TeamsConnectorConfig1.png){height="" width=""}
 -->

Teams が URL を作成するので、コピーして ProGet に追加してください。

<!--
![image](https://docs.inedo.com/resources/documentation/proget/TeamsConnectorConfig2.png){height="" width=""}
 -->
 
ProGet 内の [Manage Feed (フィード管理)] ページから [Webhooks] タブを クリックし、[create (作成する)] をクリックしてください。フィードまたは グローバルレベルの両方でウェブフックが作成できます。

Teams のメッセージ Json はカード（リンク先英語）を使用するため、設定がより複雑になります。　Microsoft のコネクタにあるドキュメントページ（リンク先英語）からサンプル構文を入手するか、以下のサンプル構文をコピー＆ペーストして Teams のWebhook をテストしてください。

```jsx
{
	"@context": "[https://schema.org/extensions](https://schema.org/extensions)",
	"@type":"MessageCard",
	"themeColor":"025291",
	"title":"ProGet Webhooks - $FeedType Package $WebhookEvent to $FeedName",
	"text": "**feed:** $FeedName, **package:** $PackageId, **version:** $PackageVersion, **hash:** $PackageHash, **packageType:** $FeedType, **event:** $WebhookEvent, **user:** $UserName"
}
```

Webhook を保存した後、ProGet で設定したアクションを実行すると、Teams に ProGet からの通知が届きます。

## その他の注意事項

個々のフィードページ、もしくは [Settings (設定)] ＞ [Webhooks] ＞ [Create Webhook (ウェブフックを作成)] からWebhookを作成できます。また、グローバルWebhookを作成するには [all feeds (すべてのフィード)] を選んでください。Dockerフィードの場合は Docker の URL が異なるため選べません。

Webhook は ProGet の全バージョンで設定が可能ですが、無料版では起動しません。