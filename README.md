# frontend-sync-target-demo
API側から同期されたファイルを受け取る検証用リポジトリ

## 同期機能について

このリポジトリは、API側リポジトリ (`ht-0328/api-sync-source-demo`) から特定のディレクトリ配下のファイルを取り込む**同期先リポジトリ**です。

- **実行方法**: GitHub Actions の `Sync from API` ワークフローを手動実行 (`workflow_dispatch`) することで同期を行います。
- **取得範囲**: `sparse-checkout` を使用し、API側リポジトリの一部である `sync-source/` 配下のみを効率的に取得します。
- **同期先**: 取り込んだファイルは、このリポジトリの `synced/api/` ディレクトリ配下に同期されます。
- **結果の反映**: 同期による差分が発生した場合、自動的に Pull Request が作成されます。差分がない場合は Pull Request は作成されません。

## GitHub上の設定手順

同期元リポジトリ `api-sync-source-demo` が private の場合でも動くようにするため、以下の「コード以外のGitHub上の設定手順」が必要です。

### 1. GitHub ActionsのWorkflow permissions設定

`frontend-sync-target-demo` 側で、GitHub Actions が同期結果をPull Request化できるようにする必要があります。

- `frontend-sync-target-demo` を開く
- Settings を開く
- Actions を開く
- General を開く
- Workflow permissions までスクロールする
- `Read and write permissions` を選択する
- `Allow GitHub Actions to create and approve pull requests` にチェックする
- Save を押す

**設定が必要な理由:**
同期Workflowが `synced/api/` や `sync-metadata/` の変更を同期用ブランチにpushし、Pull Requestを作成するためです。

### 2. Fine-grained Personal Access Token の作成手順

GitHubアカウント側で、`api-sync-source-demo` を読むための Fine-grained Personal Access Token を作成します。

- GitHub右上の自分のアイコンをクリックする
- Settings を開く
- Developer settings を開く
- Personal access tokens を開く
- Fine-grained tokens を開く
- Generate new token を押す

**PAT作成画面で指定する内容:**
- Token name: `frontend-read-api-sync-source-demo`
- Expiration: 任意。ただし検証用途なら30日程度でよい
- Repository access: `Only select repositories`
- Selected repositories: `ht-0328/api-sync-source-demo`
- Repository permissions:
  - Contents: `Read-only`

**Contents: Read-only を選ぶ理由:**
理由は、`frontend-sync-target-demo` のGitHub Actionsは `api-sync-source-demo` を読むだけで、`api-sync-source-demo` へ書き込まないためです。

> **注意:** PAT作成後に表示される `github_pat_...` の文字列は一度しか表示されないため、必ずコピーしてください。

### 3. Repository Secret の登録手順

作成したPATを `frontend-sync-target-demo` に Repository Secret として登録します。

- `frontend-sync-target-demo` を開く
- Settings を開く
- Secrets and variables を開く
- Actions を開く
- New repository secret を押す

**登録内容:**
- Name: `SOURCE_REPO_PAT`
- Secret: 作成した `github_pat_...` を貼り付ける

最後に **Add secret** を押します。

**SOURCE_REPO_PAT が必要な理由:**
理由は、`frontend-sync-target-demo` のGitHub Actionsが private の `api-sync-source-demo` を checkout するためです。

## 動作確認手順

- `api-sync-source-demo` に `sync-source/` が存在することを確認する
- `frontend-sync-target-demo` の Actions を開く
- sparse-checkout同期用のWorkflowを選択する
- Run workflow を押して手動実行する
- Workflowが成功することを確認する
- Pull requests に同期PRが作成されることを確認する
- PR内で `synced/api/` に同期元ファイルが入っていることを確認する
- `sync-metadata` などの同期元情報ファイルがある場合は、同期元リポジトリ、ref、commit hash、同期元ディレクトリが記録されていることを確認する

## 注意事項

- `SOURCE_REPO_PAT` はコードに直接書かない
- `SOURCE_REPO_PAT` は GitHub Secrets に登録する
- PATの権限は必要最小限にする
- 今回は `api-sync-source-demo` を読むだけなので Contents: Read-only にする
- PATの期限が切れた場合は、再作成して `SOURCE_REPO_PAT` を更新する
- `api-sync-source-demo` が public の場合はPATなしでも読める場合があるが、private運用を想定するなら `SOURCE_REPO_PAT` を使う
