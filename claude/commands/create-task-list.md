---
allowed-tools: Write, Read, Grep, Glob, Task, Bash, mcp__notionApi__API-post-search, mcp__notionApi__API-post-database-query, mcp__notionApi__API-retrieve-a-page
description: NotionのタスクとDocsリポジトリのADRからPR説明文用のtask listを生成
---

## Context
- 引数: $ARGUMENTS
- 現在のブランチ: !`git branch --show-current 2>/dev/null || echo "not in git repository"`
- 現在のディレクトリ: !`pwd`

## Your task

1. **入力の解析**
   - 引数からNotionタスクIDとDocsリポジトリパスを解析
   - タスクIDが指定されない場合は、現在のブランチ名から抽出
   - 例: `feat/PJT-123-jwt-auth` → `PJT-123`

2. **Notionタスクの取得**
   - StoryデータベースID: `fff371bd-abeb-8190-aa76-c897bd04df17`
   - タスクIDで検索し、親タスクとサブタスクの情報を取得

3. **Docsリポジトリの検索とADR読み込み**
   - Docsリポジトリを検索（../docs, ~/workspace/docs等）
   - 関連するADRドキュメントを特定して読み込み

4. **PR説明文の生成**
   - Markdown形式でPR説明文を作成
   - GitHub/GitLabのタスクリスト記法を使用
   - ファイル名: `.github/pull_request_template_task.md`

5. **PR説明文フォーマット**
   ```markdown
   ## 🎯 概要
   
   {Notionタスクの要約}
   
   - **Notion**: [{タスクID}](https://notion.so/...)
   - **関連ADR**: ADR-XXX, ADR-YYY
   
   ## ✅ タスクリスト
   
   ### 基盤・アーキテクチャ
   - [ ] プロジェクト基本構造の作成
   - [ ] 共通インターフェースの定義
   - [ ] 設定管理の実装
   
   ### ドメインモデル
   - [ ] {エンティティ名}エンティティの実装
   - [ ] {エンティティ名}リポジトリインターフェースの定義
   - [ ] 値オブジェクトの実装
   - [ ] ドメインサービスの実装
   
   ### ユースケース
   - [ ] {ユースケース名}の実装
   - [ ] コマンドハンドラーの実装
   - [ ] クエリハンドラーの実装
   
   ### インフラストラクチャ
   - [ ] リポジトリ実装（{DB名}）
   - [ ] 外部サービス連携（{サービス名}）
   - [ ] キャッシュ層の実装
   
   ### API
   - [ ] {エンドポイント名}エンドポイントの実装
   - [ ] リクエスト/レスポンスDTOの定義
   - [ ] バリデーションの実装
   - [ ] OpenAPI仕様の更新
   
   ### テスト
   - [ ] ドメインロジックの単体テスト
   - [ ] ユースケースの単体テスト
   - [ ] リポジトリの統合テスト
   - [ ] APIエンドポイントの統合テスト
   - [ ] 振る舞いテスト（BDD）
   
   ### ドキュメント・その他
   - [ ] API仕様書の更新
   - [ ] READMEの更新
   - [ ] マイグレーションスクリプト
   - [ ] 設定ファイルの更新
   
   ## 📋 実装方針
   
   ### ADRに基づく決定事項
   {ADRから抽出した主要な実装方針}
   
   ### 技術的詳細
   - **アーキテクチャ**: クリーンアーキテクチャ
   - **データベース**: {使用するDB}
   - **認証方式**: {認証方式}
   
   ## 🧪 テスト方針
   
   - 単体テストカバレッジ: 80%以上
   - 統合テストで主要なユースケースをカバー
   - 振る舞いテストで受け入れ条件を検証
   
   ## 📝 レビューポイント
   
   - [ ] アーキテクチャの決定事項に従っているか
   - [ ] エラーハンドリングが適切か
   - [ ] テストが十分か
   - [ ] パフォーマンスへの影響
   - [ ] セキュリティの考慮
   
   ## 🚀 デプロイ時の注意事項
   
   - [ ] 環境変数の追加が必要
   - [ ] データベースマイグレーションの実行
   - [ ] 関連サービスの設定更新
   ```

6. **ファイルの保存**
   - `.github/pull_request_template_task.md`として保存
   - または`pr_description.md`として一時保存

7. **使用方法の案内**
   ```
   生成されたPR説明文を以下の場所に保存しました:
   📄 .github/pull_request_template_task.md
   
   【使用方法】
   1. PR作成時にこのテンプレートを選択
   2. または内容をコピーしてPR説明文に貼り付け
   3. 実装完了したタスクにチェックを入れる
   
   【GitHub CLI使用例】
   gh pr create --title "feat: {機能名}" --body-file .github/pull_request_template_task.md
   ```

## 実行結果の例
```
PR説明文用のタスクリストを生成しました！

📋 生成内容:
- Notionタスク: PJT-123 - JWT認証の実装
- 関連ADR: 3件
- タスク項目: 28個
- 保存先: .github/pull_request_template_task.md

このファイルをPR作成時に使用してください。
実装が完了したらチェックボックスにチェックを入れていきましょう。
```

## 使用例
- `/user:create-task-list` - 現在のブランチから自動生成
- `/user:create-task-list PJT-123` - 特定のNotionタスクから生成
- `/user:create-task-list PJT-123 ~/workspace/docs` - Docsパス指定