---
allowed-tools: Write, Read, Grep, Glob, Task, Bash, mcp__notionApi__API-post-search, mcp__notionApi__API-post-database-query, mcp__notionApi__API-retrieve-a-page, mcp__notionApi__API-get-blocks-children
description: NotionページからADRを生成し、ブランチ作成からPR作成まで自動化
---

## Context
- 引数: $ARGUMENTS
- 現在のディレクトリ: !`pwd`
- 今日の日付: !`date +%Y-%m-%d`
- 現在のブランチ: !`git branch --show-current 2>/dev/null || echo "not in git repository"`

## Your task

1. **入力の解析**
   - 引数からNotionページのURLまたはページIDを解析
   - ADR番号を引数から取得（未指定の場合は既存ADRから連番を決定）

2. **Notionページの取得**
   - ページIDを使用してNotionページを取得
   - ページ内のブロックコンテンツを全て取得
   - 階層構造を保持してコンテンツを解析

3. **既存ADRの確認**
   - `./design/`ディレクトリ内のADRファイルを検索
   - 既存のPJT番号（PJT-XXX形式）を確認して次の番号を決定

4. **Gitブランチの作成**
   - Gitflowルールに従ってブランチを作成
   - ブランチ名: `feat/PJT-{番号}`
   - developブランチから新規ブランチを作成してチェックアウト
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feat/PJT-{番号}
   ```

5. **ADRの生成**
   - プロジェクトのテンプレート（./design/template.md）に基づいてADRを生成
   - Notionページの内容を適切なセクションにマッピング

6. **ADRテンプレートへのマッピング**
   ```markdown
   # {Notionページのタイトル}

   * ステータス: 運用中
   * 日付: {今日の日付}
   * 提案者: {Notionページの作成者または引数で指定}
   * 技術的背景/関連イシュー: PJT-{番号}

   ## 1. 背景と課題
   {Notionページの「背景」「課題」「問題」セクションの内容}

   ## 2. 前提条件・制約条件
   {Notionページの「前提」「制約」「要件」セクションの内容}

   ## 3. 検討したソリューションの選択肢
   {Notionページの「選択肢」「案」「オプション」セクションの内容を整形}

   1. {選択肢1の名前}
      - {選択肢1の詳細}
   2. {選択肢2の名前}
      - {選択肢2の詳細}

   ## 4. 決定したソリューションと正当化

   ### 採用するソリューション
   {Notionページの「決定」「採用」セクションから抽出}

   ### 正当化
   {選択理由、評価結果}

   ### 詳細
   {実装の詳細、技術的な仕様}

   ## 5. 対応すべき主要ISSUEと受け入れ基準
   {Notionページの「タスク」「TODO」セクションから生成}

   * {Issueのタイトル}
     * 概要
       * {概要}
     * 受け入れ基準
       * {基準}

   ## 6. 懸念事項・リスクと対応策
   {Notionページの「リスク」「懸念」セクションの内容}

   * {リスク1}
     * 詳細
       * {詳細}
     * 対応案
       * {対応案}

   ## 7. 将来の検討事項 (Optional)
   {Notionページの「将来」「今後」セクションの内容}

   ## 8. 補足 (Optional)
   {その他の情報}

   ## 9. 関連リンク
   * [Notion: {ページタイトル}]({NotionページURL})
   {その他の関連リンク}

   ---
   *このADRは一度承認されると、その内容は原則として不変です。変更が必要な場合は、新しいADRでこのADRを「廃止 (Superseded)」として参照してください。*
   ```

7. **ファイルの保存**
   - ディレクトリ作成: `./design/PJT-{番号}/`
   - ファイル名: `{ページタイトル}.md`（日本語OK）
   - 保存先: `./design/PJT-{番号}/{ページタイトル}.md`

8. **コミットの作成**
   ```bash
   git add ./design/PJT-{番号}/
   git commit -m "[feat]PJT-{番号}: {タイトル}の技術決定

   - Notionページから技術決定記録を作成
   - {主要な決定事項の要約}

   🤖 Generated with Claude Code

   Co-Authored-By: Claude <noreply@anthropic.com>"
   ```

9. **リモートへのプッシュ**
   ```bash
   git push -u origin feat/PJT-{番号}
   ```

10. **Pull Requestの作成**
    - Gitflowルールに従ってdevelopブランチへのPRを作成
    ```bash
    gh pr create --base develop --title "[feat]PJT-{番号}: {タイトル}の技術決定" --body "## 関連Issue/ADR
    - PJT-{番号}: {タイトル}
    - [Notion: {ページタイトル}]({NotionページURL})

    ## 1. 目的・背景 (Purpose / Background)
    {背景の要約}

    ## 2. 変更概要 (Summary of Changes)
    - ADR（Architecture Decision Record）を新規作成
    - {決定事項の要約}

    ## 3. 検証・テスト方法 (How to Verify / Test)
    - ADRの内容が正確にNotionページを反映していることを確認
    - テンプレート（design/template.md）に準拠していることを確認
    - 技術的な決定事項が明確に記載されていることを確認

    ## 4. 影響範囲 (Impact Analysis)
    - ドキュメントのみの変更（コードへの影響なし）

    ## 5. スクリーンショット・動画 (UI変更がある場合) (Screenshots / Screen Recordings)
    該当なし

    ## 6. マージターゲットと考慮事項 (Merge Target & Considerations)
    - ターゲットブランチ
      - [ ] main/master 
      - [x] develop
      - [ ] fix
      - [ ] release
    - 考慮事項
      - レビュー後にマージ

    ## 7. 補足事項・未解決の質問 (任意) (Additional Notes / Open Questions)
    このADRは技術検討の結果を文書化したものです。実装時には詳細な設計が必要になる場合があります。

    🤖 Generated with Claude Code"
    ```

11. **使用方法の案内**
    ```
    ADRの作成とPR作成が完了しました！

    📄 生成されたADR:
    - ファイル: ./design/PJT-{番号}/{タイトル}.md
    - タイトル: {タイトル}
    - ステータス: 運用中

    🌿 Gitの操作:
    - ブランチ: feat/PJT-{番号} (develop から分岐)
    - コミット: ✅ 完了
    - プッシュ: ✅ 完了

    🔗 Pull Request:
    - URL: {PRのURL}
    - ベースブランチ: develop
    - タイトル: [feat]PJT-{番号}: {タイトル}の技術決定

    【次のステップ】
    1. PRのURLをクリックしてブラウザで開く
    2. テックリードにレビューを依頼
    3. フィードバックに基づいて必要に応じて修正
    4. 承認後、スカッシュマージでdevelopにマージ
    ```

## 実行結果の例
```
NotionページからADRを生成し、PRを作成しました！

📋 生成内容:
- Notionページ: JWT認証の技術検討
- ADR番号: PJT-931
- ファイル: ./design/PJT-931/JWT認証の技術検討.md

🌿 Git操作:
- ブランチ: feat/PJT-931 (develop → feat/PJT-931) ✅
- コミット: 1件 ✅
- プッシュ: 完了 ✅

🔗 Pull Request:
- https://github.com/owner/repo/pull/123
- develop ← feat/PJT-931
- レビュー待ち

テックリードにレビューを依頼してください。
```

## 使用例
- `/user:create-adr-from-notion https://notion.so/...` - NotionページURLから生成
- `/user:create-adr-from-notion page-id-123` - ページIDから生成
- `/user:create-adr-from-notion https://notion.so/... 931` - ADR番号を指定して生成
- `/user:create-adr-from-notion https://notion.so/... 931 "開発チーム"` - 提案者も指定