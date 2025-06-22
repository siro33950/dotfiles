---
name: push
description: 
---
# Git Push

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
```
