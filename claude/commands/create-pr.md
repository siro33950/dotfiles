---
name: create-pr
description: 
---
# Pull Request作成

このコマンドを実行すると、以下の処理を自動的に行います：

1. **変更内容の確認**
   - `git status`で変更ファイルを確認
   - `git diff`で変更内容を確認

2. **コミットの作成**
   - 全ての変更をステージング（`git add .`）
   - 変更内容に基づいた適切なコミットメッセージを生成
   - コミットを作成

3. **リモートへのプッシュ**
   - 現在のブランチをリモートにプッシュ
   - 初回プッシュの場合は`-u`オプションで上流ブランチを設定

4. **Pull Requestの作成**
   - `gh pr create`を使用してPRを作成
   - タイトルとボディを自動生成
   - 変更内容のサマリーを含める

### 実行される処理の例
```bash
# 1. 状態確認
git status
git diff

# 2. コミット
git add .
git commit -m "feat: 実装した機能の説明

詳細な変更内容...

🤖 Generated with Claude Code

Co-Authored-By: Claude <noreply@anthropic.com>"

# 3. プッシュ
git push -u origin feature/branch-name

# 4. PR作成
gh pr create --title "feat: 実装した機能" --body "## 概要
- 変更内容のサマリー

## テストプラン
- [ ] テスト項目1
- [ ] テスト項目2

🤖 Generated with Claude Code"
```
