---
title: "Flutter Web アプリを App Engine にデプロイするまでの道のり"
emoji: "🗂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Flutter, GoogleAppEngine]
published: false
---

最近、とあるハッカソン用アプリの開発中に，Flutter Web アプリを Google App Engine にデプロイしようとしたところ、いくつかのエラーに遭遇しました。Gemininiに相談しながら行ったのですが，途中古い情報で戸惑ったりしたので，それらのエラーと解決策をまとめます。

## 前提
- Flutter Web アプリケーション
- Google App Engine スタンダード環境 (Python 3)


## 発生したエラーと解決策
1. Python 2.7 ランタイムのサポート終了

```
(gcloud.app.deploy) INVALID_ARGUMENT: Error(s) encountered validating runtime. Runtime python27 is end of support and no longer allowed. Please use the latest Python runtime for App Engine Standard..
```
app.yaml の runtime を python310 に変更。

2. threadsafe フィールドの非推奨化

```
This field is not supported with runtime [python310] and can safely be removed.
```

app.yaml から threadsafe: true を削除。

3. skip_files フィールドの廃止

```
(gcloud.app.deploy) skip_files cannot be used with the [python310] runtime. Ignore patterns are instead expressed in a .gcloudignore file.
```

.gcloudignore ファイルを作成し、無視するファイルを指定。app.yaml から skip_files を削除。

4. api_version フィールドの削除

```
ERROR: (gcloud.app.deploy) INVALID_ARGUMENT: Error(s) encountered validating runtime. "api_version" field is not allowed in runtime python310.
```

app.yaml から api_version を削除。

## まとめ
これらのエラーを解決することで、Flutter Web アプリケーションを Google App Engine にデプロイすることができました。同様の問題に遭遇した方の参考になれば幸いです。