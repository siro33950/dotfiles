---
allowed-tools: Read, Grep, Glob, Task, Bash, mcp__notionApi__API-post-search, mcp__notionApi__API-post-database-query, mcp__notionApi__API-retrieve-a-page, UserCommand
description: NotionのタスクとDocsリポジトリのADRからPR説明文用のtodo listを生成
---

## Context
- 引数: $ARGUMENTS
- 現在のブランチ: !`git branch --show-current 2>/dev/null || echo "not in git repository"`
- 現在のディレクトリ: !`pwd`

## Your todo

1. **入力の解析**
   - 引数からNotionタスクID、Docsリポジトリパス、出力オプションを解析
   - タスクIDが指定されない場合は、現在のブランチ名から抽出
   - 例: `feat/PJT-123-jwt-auth` → `PJT-123`
   - 出力オプション:
     - `--file` または `-f`: MDファイルとして出力
     - `--output` または `-o`: 出力ファイル名を指定（デフォルト: `pr_todo_list.md`）

2. **Notionタスクの取得**
   - StoryデータベースID: `fff371bd-abeb-8190-aa76-c897bd04df17`
   - タスクIDで検索し、親タスクとサブタスクの情報を取得

3. **Docsリポジトリの検索とADR読み込み**
   - Docsリポジトリを検索（../docs, ~/workspace/docs等）
   - 関連するADRドキュメントを特定して読み込み

4. **PR説明文の生成と更新**
   - 現在のPRの状態を確認（`gh pr view`）
   - PRが存在する場合:
     - 既存のPR説明文を取得（`gh pr view --json body`）
     - TODOリストセクションを追加または更新
     - `gh pr edit --body`でPR説明文を更新
   - PRが存在しない場合:
     - TODOリストを含む完全なPR説明文を生成
     - `create-pr`コマンドを実行してドラフトPRを作成:
       - コミットが必要な場合は自動でコミット
       - TODOリストを含むPR説明文でドラフトPRを作成
       - `--draft`フラグを使用
       - 作成されたPRのURLを表示

5. **PR説明文フォーマット**
   ```markdown
   ## 🎯 概要
   
   {Notionタスクの要約}
   
   - **Notion**: [{タスクID}](https://notion.so/...)
   - **関連ADR**: ADR-XXX, ADR-YYY
   
   ## ✅ TODOリスト
   
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

6. **出力処理**
   - ファイル出力モード（`--file`フラグあり）:
     - 指定されたファイル名（デフォルト: `pr_todo_list.md`）に保存
     - 保存先とファイル内容の確認方法を案内
   - PR更新モード（デフォルト）:
     - 既存PRがある場合: `gh pr edit --body`で更新
     - 新規PRの場合: `create-pr --draft`でドラフトPR作成

7. **実行結果の表示**
   ```
   PRの説明文を更新しました！
   
   【更新内容】
   ✅ TODOリスト: 28項目
   📝 関連ADR: 3件
   🔗 PR URL: https://github.com/org/repo/pull/123
   
   【次のステップ】
   1. 実装を進めながらTODOにチェックを入れる
   2. `gh pr view --web`でブラウザで確認
   3. 進捗に応じてTODOリストを更新
   ```

## 実行結果の例
```
PR説明文用のTODOリストを生成しました！

📋 生成内容:
- Notionタスク: PJT-123 - JWT認証の実装
- 関連ADR: 3件
- TODO項目: 28個
- 更新方法: gh pr editで直接更新

このファイルをPR作成時に使用してください。
実装が完了したらチェックボックスにチェックを入れていきましょう。
```

## 使用例
- `/user:create-todo-list` - 現在のブランチから自動生成してPRを更新
- `/user:create-todo-list PJT-123` - 特定のNotionタスクから生成してPRを更新
- `/user:create-todo-list PJT-123 ~/workspace/docs` - Docsパス指定
- `/user:create-todo-list --file` - MDファイルとして出力（pr_todo_list.md）
- `/user:create-todo-list PJT-123 --file -o checklist.md` - 指定ファイルに出力