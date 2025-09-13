## GCP (Google Cloud Platform)

このディレクトリには、GCP関連の設定ファイルが含まれています。

### cloudbuild.yml

Google Cloud Build を使用して、アプリケーションのCI/CDパイプラインを定義するファイルです。
この設定ファイルに基づいて、Dockerイメージのビルドやコンテナレジストリへのプッシュなどが行われます。

### 実行方法

以下のコマンドを使用して、手動でビルドをトリガーできます。

```bash
gcloud builds submit --config gcp/cloudbuild.yml .
```

（`gcloud` CLIがインストールされ、認証が完了している必要があります。）
