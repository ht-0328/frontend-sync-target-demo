# AIエージェント向け基本ルール

このリポジトリで作業するAIエージェントは、以下のルールに従って作業を行ってください。

## 作業の基本方針
- 変更理由、実現方法、影響範囲が分かるように作業すること
- 目的に関係ないファイルは変更しないこと
- 不要に大きい変更を避けること
- 既存の構成や命名を尊重すること

## 言語ルール
- 以下の項目は**日本語**で書くこと:
  - README
  - 作成するドキュメント
  - コメント
  - Pull Request本文
  - Pull Requestタイトル
  - コミットメッセージの description 部分
- ただし、以下の機械的に使う名前は**英数字のまま**でよいこと:
  - ファイル名
  - ディレクトリ名
  - ブランチ名
  - GitHub Actionsの構文
  - Secret名
  - JSONキー名
  - 外部Action名など

## コミットメッセージルール
コミットメッセージは **Conventional Commits 1.0.0** に合わせてください。

### 形式
```
<type>[optional scope]: <description>
```
- `<type>` は英語のままとすること
- `<description>` は日本語で書くこと

### type の種類
原則として以下を使用してください。
- `feat`: 新規機能
- `fix`: 不具合修正
- `docs`: ドキュメント変更
- `ci`: GitHub ActionsなどCI設定の変更
- `chore`: 雑務・設定・生成物など
- `refactor`: 振る舞いを変えない整理
- `test`: テスト追加・修正

### 例
- `docs: AGENTS.mdを追加`
- `docs: PRテンプレートを追加`
- `ci: 同期Workflowを追加`
- `feat: sparse-checkoutによる同期機能を追加`
- `fix: 同期Workflowの構文エラーを修正`
- `refactor: 同期処理の構成を整理`

## PR作成ルール
Pull Requestを作成する際は、以下のルールに従ってください。
- Pull Requestのタイトルは日本語にする
- Pull Requestのタイトルにも Conventional Commits の type を使う (例: `docs: AGENTS.mdとPRテンプレートを追加`)
- Pull Request本文は `.github/pull_request_template.md` に従って書くこと
- テンプレートの各項目を空欄のままにしないこと
- 該当しない項目は「該当なし」と書くこと
- 変更種別を必ず1つ以上選ぶこと
- 変更種別は「日本語名 / type」の形式で分かるようにすること
- 受け入れ条件を明確に書くこと
- 動作確認した内容があれば必ず書くこと
