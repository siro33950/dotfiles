---
name: release
description: 
---
# リリースPR作成

## 概要
リリースブランチからmaster/developブランチへのPRを作成する手順書です。

## 前提条件
- 現在のブランチが `release/` で始まるリリースブランチであること
- リモートにプッシュ済みであること

## コマンド

### 1. 現在のブランチ確認
```bash
git branch --show-current
```

### 2. リリースブランチからmasterへのPR作成
```bash
gh pr create --base master --title "Release: $(git branch --show-current | sed 's/release\///')" --body "$(cat <<'EOF'
## リリース内容
$(git branch --show-current | sed 's/release\///')のリリース

## 変更内容
$(git log master..HEAD --oneline)

## チェックリスト
- [ ] すべてのテストが通過
- [ ] developブランチからの変更が含まれている
- [ ] リリースノートの確認

🤖 Generated with [Claude Code](https://claude.ai/code)
EOF
)"
```

### 3. リリースブランチからdevelopへのPR作成
```bash
gh pr create --base develop --title "Merge back: $(git branch --show-current | sed 's/release\///')" --body "$(cat <<'EOF'
## 概要
リリースブランチの変更をdevelopにマージバック

## 変更内容
$(git log develop..HEAD --oneline)

## 目的
- リリースブランチでの修正をdevelopに反映
- ブランチの同期を保つ

🤖 Generated with [Claude Code](https://claude.ai/code)
EOF
)"
```
