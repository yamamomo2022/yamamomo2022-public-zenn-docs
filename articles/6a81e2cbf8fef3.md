---
title: "Flutter Web アプリを App Engine にデプロイするまでの道のり"
emoji: "🗂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Flutter, GoogleAppEngine]
published: true
---

最近、とあるハッカソン用アプリの開発中に，Flutter Web アプリを Google App Engine にデプロイしようとしたところ、いくつかのエラーに遭遇しました。Geminiに相談しながら行ったのですが，途中古い情報で戸惑ったりしたので，それらのエラーと解決策をまとめます。

## 前提
- Flutter Web アプリケーション
- Google App Engine スタンダード環境 (Python 3)

### はじめの設定

初回は以下の設定で行いましたが，端的に古かったようです.

```yaml:app.yaml
runtime: python27 
api_version: 1
threadsafe: true

handlers:
  - url: /
    static_files: build/web/index.html 
    upload: build/web/index.html

  - url: /(.*)
    static_files: build/web/\1
    upload: build/web/(.*)
```

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


### 最終的な設定
修正結果です．

```yaml:app.yaml
runtime: python310
  
handlers:
  - url: /
    static_files: build/web/index.html
    upload: build/web/index.html

  - url: /(.*)
    static_files: build/web/\1
    upload: build/web/(.*)
```

## まとめ
これらのエラーを解決することで、Flutter Web アプリケーションを Google App Engine にデプロイすることができました。同様の問題に遭遇した方の参考になれば幸いです。