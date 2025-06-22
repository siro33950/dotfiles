---
allowed-tools: Bash, mcp__notionApi__API-post-search, mcp__notionApi__API-post-database-query, mcp__notionApi__API-retrieve-a-page
description: 現在のGitブランチに関連するNotionタスクとサブタスクを取得（スプリント指定可能）
---

## Context
- 現在のブランチ: !`git branch --show-current 2>/dev/null || echo "not in git repository"`
- 現在の年月: !`date +%Y-%m`
- 引数: $ARGUMENTS

## Your task

1. **Gitブランチ名を確認**
   - Gitリポジトリでない場合は、その旨を表示して終了

2. **引数を解析**
   - 引数からスプリント（YYYY-MM形式）とタスクIDを識別
   - 例：`2025-06` はスプリント、`PJT-123` はタスクID
   - スプリントが指定されていない場合は、現在の年月を使用

3. **検索キーを決定**
   - 引数にタスクIDがある場合：そのIDを使用
   - 引数にタスクIDがない場合：ブランチ名から以下のパターンで抽出
     - `feat/PJT-XXX` → `PJT-XXX`
     - `fix/PJT-XXX-description` → `PJT-XXX`
     - `PJT-XXX` → `PJT-XXX`
     - その他：ブランチ名全体を検索キーとする

4. **NotionのStoryデータベース（ID: fff371bd-abeb-8190-aa76-c897bd04df17）で検索**
   - PJT-XXX形式の場合：IDプロパティ（unique_id）で検索
     - IDプロパティの値は "PJT-XXX" 形式で保存されている
     - 完全一致で検索（prefix: "PJT", number: XXX）
     - **ID指定時はSprintフィルターは適用しない**（全期間から検索）
   - それ以外の場合：Todoフィールド（タイトル）で部分一致検索
     - Sprint = 指定されたスプリント（例："2025-06"）でフィルタリング

5. **親タスクが見つかった場合、サブタスクも取得**
   - 親プロジェクトのrelationを使用して、サブタスクを検索
   - サブプロジェクトフィールドから関連タスクを取得

6. **結果の表示**
   - 検索条件（スプリント、検索キー）を最初に表示
   
   **親タスク情報：**
   - **タスクID**: PJT-XXX
   - **タイトル**: タスクのタイトル
   - **ステータス**: 現在のステータス（Todo/In-Progress/Review/Done等）
   - **担当者**: アサインされている人
   - **優先度**: 優先度と重要度
   - **種別**: 開発、バグ改修、作業など
   - **対象**: 対象システム（revenue-server等）
   - **要約**: タスクの詳細説明
   - **関連PR**: GitHubプルリクエストのリンク（あれば）
   
   **サブタスク一覧：**（存在する場合）
   - 各サブタスクを以下の形式で表示
     - [ ] または [x]（ステータスに応じて）
     - タスクID - タイトル（担当者）- ステータス
   
   - 複数の親タスクが見つかった場合：すべてをリスト表示
   - タスクが見つからない場合：その旨を表示

7. **エラーハンドリング**
   - NotionAPIのエラーは分かりやすく表示
   - 結果は見やすくフォーマット
   - サブタスクは親タスクの下にインデントして表示

## 使用例
- `/user:branch-tasks` - 現在のスプリントで検索
- `/user:branch-tasks 2025-05` - 2025年5月のスプリントで検索
- `/user:branch-tasks PJT-123` - 全期間からPJT-123を検索（ID検索時はスプリント指定不要）
- `/user:branch-tasks 2025-06 PJT-123` - 2025年6月のスプリントでPJT-123を検索（この場合もID優先で全期間検索）