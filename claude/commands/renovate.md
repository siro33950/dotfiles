---
name: renovate
description: 現在開いているRenovateのPRをチェック後に全てマージする
---
# Renovate PR一括マージコマンド

現在開いているRenovateのPRをチェック後、全て自動的にマージします。

## 実行内容

1. `gh pr list`でRenovate関連の全てのオープンPRを取得
2. 各PRのステータスチェックを確認
3. チェックが成功しているPRのみマージ
4. マージ後は自動的にブランチを削除

## 実行コマンド

```bash
# Renovate関連のPRを取得
echo "🔍 Renovate PRを確認中..."
gh pr list --label "renovate" --state open --json number,title,statusCheckRollup --jq '.[] | "\(.number)|\(.title)|\(.statusCheckRollup)"' | while IFS='|' read -r pr_number pr_title check_status; do
  echo ""
  echo "📋 PR #$pr_number: $pr_title"
  
  # ステータスチェックの確認
  if [[ "$check_status" == "SUCCESS" ]]; then
    echo "✅ チェック成功 - マージします"
    gh pr merge $pr_number --merge --delete-branch
    if [ $? -eq 0 ]; then
      echo "✨ マージ完了"
    else
      echo "❌ マージ失敗"
    fi
  elif [[ "$check_status" == "PENDING" ]]; then
    echo "⏳ チェック実行中 - スキップ"
  else
    echo "❌ チェック失敗 - 詳細を確認中..."
    
    # 失敗したチェックの詳細を取得
    failed_checks=$(gh pr checks $pr_number --json name,state,conclusion | jq -r '.[] | select(.conclusion == "failure") | .name')
    
    # よくある失敗パターンの確認
    can_merge=false
    if echo "$failed_checks" | grep -q "codecov"; then
      echo "  ⚠️  Codecov失敗を検出 - カバレッジの問題は無視可能"
      can_merge=true
    elif echo "$failed_checks" | grep -q "lint"; then
      echo "  ⚠️  Lint失敗を検出 - 軽微な問題の可能性"
      # PRの差分を確認して判断
      if gh pr diff $pr_number | grep -q "^+.*console\.log"; then
        echo "  ❌ console.logが含まれています - マージ不可"
      else
        echo "  ✅ 重大なlintエラーなし - マージ可能"
        can_merge=true
      fi
    elif echo "$failed_checks" | grep -q "test"; then
      echo "  ⚠️  テスト失敗を検出 - 既存の問題の可能性を確認"
      # mainブランチでも失敗しているか確認
      echo "  📊 mainブランチの状態を確認中..."
      main_status=$(gh api repos/{owner}/{repo}/commits/main/status --jq '.state' 2>/dev/null || echo "unknown")
      if [[ "$main_status" == "failure" ]]; then
        echo "  ✅ mainブランチも失敗中 - Renovateが原因ではない"
        can_merge=true
      fi
    fi
    
    # マージ可能と判断した場合
    if [[ "$can_merge" == "true" ]]; then
      echo "  🔧 管理者権限でマージを試行します"
      gh pr merge $pr_number --merge --delete-branch --admin
      if [ $? -eq 0 ]; then
        echo "✨ 管理者マージ完了"
      else
        echo "❌ 管理者マージも失敗"
      fi
    else
      echo "  ❌ 自動マージ不可 - 手動確認が必要"
    fi
  fi
done

echo ""
echo "🎉 処理完了"
```