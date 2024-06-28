---
title: "PyPI (Python) フィードのホスト"
order: 5
---

Python Package Index（[PyPI](https://pypi.org/)）は、Pythonのソフトウェアとライブラリのリポジトリです。

ProGet v5.2 以降は、ProGetをインストールしたサーバに PyPI フィードをホストして、PyPI のパッケージをインストール、アップデート、アンインストールできます。

## **パッケージのインストール**

PyPI パッケージは、pip を使用してインストールします。（参照：[pip - The Python Package Installer](https://pip.pypa.io/en/stable/)） ProGet フィードからパッケージをインストールするには、次のコマンドを使用します。

```powershell
pip install {package-name}=={package-version} -i http://{proget-server}/pypi/{feed-name}/simple
```

## **パッケージの作成**

ProGetでホストできるパッケージを作成する方法については、 Pythonプロジェクトのパッケージ化チュートリアルをご参照ください。（参照：[Packaging Python Projects](https://packaging.python.org/tutorials/packaging-projects/)）

## **パッケージの公開**

**Twine**

PyPIパッケージは、Twineを使ってProGetにPush (プッシュ) します。（参照：[twine 3.2.0](https://pypi.org/project/twine/)）

ProGetフィードにパッケージをPush (プッシュ) するには、次のコマンドを使用します。

```powershell
twine upload --repository-url http://{proget-server}/pypi/{feed-name}/legacy <dist>
```

**cURL**

PyPIパッケージは、次の構文を使用してcURLでもProGetに公開できます。

```powershell
curl https://{proget-server}/pypi/{feed-name}/upload/<packageName-version.tar.gz> --user <user>:<password> --upload-file <packageName-version.tar.gz>
```

## 技術的な制限

- ProGetは、（PyPI.orgに実装されている）XML-RPC APIでの、パッケージ名とサマリーフィールドによる検索のみをサポートしています。この API の他のユースケースは、ProGet ではサポートしていません。
- その他の PyPI フィードへのコネクタでは、PyPI JSON API を実装していることが必要です。（ProGet と [PyPI.org](http://pypi.org) の両方がサポートしています）
- パッケージ名は通常（ PEP503の仕様に従って）正規化されていますが、バージョンが「-」と「_」とでだけ異なるパッケージ（ **my-package1.0** や**my_package1.1**など）は、**my-package**には正しくリストされていません。さらに、UIベースの検索は "_"と "-" を同一のものとして扱いません。（参照：[PEP 503 -- Simple Repository API](https://www.python.org/dev/peps/pep-0503/)）

これらの制限でご不便をおかけする場合には、弊社にお知らせください。修正や対処法を検討いたします。