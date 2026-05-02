# GCP デプロイガイド

本ドキュメントは、GitHub Actions を使用して Google Cloud Platform (GCP) の Cloud Run にアプリケーションをデプロイするための手順と事前準備について説明します。

## 1. 事前に必要なもの (Prerequisites)

GCP へのデプロイを行う前に、以下の準備を各自で行う必要があります。

- **GCP プロジェクト**: デプロイ先のプロジェクトを作成します。
- **課金設定**: プロジェクトに有効な請求先アカウントが紐付いている必要があります。
- **Workload Identity Federation の設定**: GitHub Actions から GCP へ安全に認証するための設定。
- **GitHub Actions 用デプロイサービスアカウント**: GitHub Actions が GCP のリソースを作成・操作するためのサービスアカウント。
  - 必要なロール:
    - `roles/editor` または必要な各種リソースを作成・更新できる十分な権限 (API有効化、Service Account作成、IAM付与、Artifact Registry作成、GCS作成、Cloud Runデプロイ)
- **GitHub Repository Variables の設定**:
  - `GCP_PROJECT_ID`
  - `GCP_REGION`
  - `GCP_WORKLOAD_IDENTITY_PROVIDER`
  - `GCP_DEPLOY_SERVICE_ACCOUNT`
  - `ARTIFACT_REPOSITORY`
  - `IMAGE_NAME`
  - `GCS_BUCKET_NAME`
  - `CLOUD_RUN_JOB_NAME`
  - `BUILD_SERVICE_ACCOUNT_NAME`
  - `RUNTIME_SERVICE_ACCOUNT_NAME`

## 2. GitHub Actions が自動作成するもの

デプロイ用のワークフロー (`.github/workflows/deploy-gcp.yml`) は、実行時に以下のリソースや設定を「存在しなければ作成する」形で自動的にセットアップします。

- **必要な GCP API の有効化**
  - `serviceusage.googleapis.com`, `iam.googleapis.com`, `iamcredentials.googleapis.com`, `cloudbuild.googleapis.com`, `artifactregistry.googleapis.com`, `run.googleapis.com`, `storage.googleapis.com`
- **Artifact Registry リポジトリ**
  - Docker 形式のイメージ保存用リポジトリ。
- **Cloud Storage (GCS) バケット**
  - アプリケーションが設定ファイルやデータを出力・参照するためのマウント用バケット。
- **Cloud Build 用サービスアカウント**
  - コンテナビルドおよび Artifact Registry へのプッシュを行うためのサービスアカウント。
- **Cloud Run 実行用サービスアカウント**
  - Cloud Run Job として実行されるアプリケーションが使用するサービスアカウント。
- **必要な IAM 権限の付与**
  - Cloud Build 用 SA への `roles/artifactregistry.writer`, `roles/logging.logWriter`, `roles/storage.objectAdmin` 付与。
  - Cloud Run 実行用 SA への GCS バケットに対するアクセス権限の付与。
- **Cloud Run Job の作成・更新**

## 3. 設定ファイルと環境変数の優先順位について

アプリケーションの設定値は、以下の優先順位で決定されます：

1. **環境変数** (例: `APP_INTERVAL`, `TRADING_SYMBOL`)
2. **YAML設定ファイル** (例: `application-gmo.yaml`)
3. **アプリケーション側のデフォルト値**

この仕組みにより、以下の柔軟な運用が可能です：

* ローカル開発では従来どおり YAML 設定ファイルを使用できます。
* Cloud Run Job では設定ファイルがなくても起動でき、本当に必要な設定値だけを環境変数で上書きできます。
* Cloud Run Job に渡す環境変数は、原則として `APP_DATA_DIR=/mnt/gcs/data` のみで動作します。

> **Note:** APIキーやAPIシークレットなどの秘密情報はこの優先順位の対象外です。将来的には Secret Manager などを使用して安全に管理する予定です。
