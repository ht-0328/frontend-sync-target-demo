# frontend-sync-target-demo
API側から同期されたファイルを受け取る検証用リポジトリ

## 同期機能について

このリポジトリは、API側リポジトリ (`ht-0328/api-sync-source-demo`) から特定のディレクトリ配下のファイルを取り込む**同期先リポジトリ**です。

- **実行方法**: GitHub Actions の `Sync from API` ワークフローを手動実行 (`workflow_dispatch`) することで同期を行います。
- **取得範囲**: `sparse-checkout` を使用し、API側リポジトリの一部である `sync-source/` 配下のみを効率的に取得します。
- **同期先**: 取り込んだファイルは、このリポジトリの `synced/api/` ディレクトリ配下に同期されます。
- **結果の反映**: 同期による差分が発生した場合、自動的に Pull Request が作成されます。差分がない場合は Pull Request は作成されません。

### 動作確認手順

1. `ht-0328/api-sync-source-demo` 側の `sync-source/` ディレクトリにデモ用ファイルが存在することを確認します。
2. このリポジトリ ( `frontend-sync-target-demo` ) の **Actions** タブから `Sync from API` ワークフローを手動実行 (`Run workflow`) します。
3. ワークフロー実行後、`synced/api/` 配下にファイルが同期される Pull Request が作成されることを確認します。
4. 作成された Pull Request 内で、同期元情報の記録ファイル (`synced/sync-info.json`) も併せて作成・更新されていることを確認します。

> [!NOTE]
> 自動的に Pull Request を作成するため、リポジトリの **Settings > Actions > General > Workflow permissions** にて `Read and write permissions` と `Allow GitHub Actions to create and approve pull requests` が許可されている必要があります。
