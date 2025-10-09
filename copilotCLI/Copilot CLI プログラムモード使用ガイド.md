# GitHub Copilot CLI プログラムモード（非インタラクティブモード）使用ガイド

GitHub Copilot CLIには、インタラクティブなTUIモードの他に、コマンドラインからスクリプトや自動化で使用できるプログラムモード（非インタラクティブモード）が提供されています。

## ? 基本的な使用方法

### プロンプトモード
```bash
copilot -p "プロンプトの内容"
```

**例:**
```bash
copilot -p "Create a Node.js express server with a health check endpoint"
copilot -p "Analyze this log file and find all error messages"
copilot -p "Write a Python script to fetch data from an API"
```

## ? 発見の経緯

GitHub issue #96「Add non-interactive exec and input prompt support」で、ユーザーがCodex CLIのような非インタラクティブ機能を要求した際、GitHubの開発チーム（@lostintangent）から以下の回答がありました：

> "If you run `copilot -p <prompt>`, that allows you to run prompts headlessly (vs. with the TUI)."

この機能は既に実装されていましたが、ドキュメントが不十分で発見しづらい状態でした。

## ? 使用例とユースケース

### 1. ログ解析
```bash
cat app.log | copilot -p "Find all lines with status 500 and count them"
```

### 2. プロジェクトの足場作成
```bash
copilot -p "Initialize a Node.js project, install express, and add a GET /health route"
```

### 3. jqコマンド生成支援
```bash
curl -s https://api.example.com/items | copilot -p "Write a jq command to extract id and name fields"
```

### 4. 安全なリポジトリ検索
```bash
copilot -p "Create a grep command to find TODOs excluding node_modules directory"
```

### 5. Python環境のクイックセットアップ
```bash
copilot -p "Create a virtual environment, install requests library, and fetch data from https://example.com"
```

## ? パイプライン統合

### 標準入力からの読み込み
プロンプトに `-` を指定することで、標準入力からプロンプトを読み込むことができます：

```bash
echo "Explain this error message" | copilot -p -
```

### ファイルからのプロンプト読み込み
```bash
copilot -p - < prompt.txt
```

### 複雑なパイプライン例
```bash
# ログファイルを解析してエラーを抽出
tail -100 /var/log/app.log | copilot -p "Summarize the errors and suggest fixes"

# Gitログを分析
git log --oneline -10 | copilot -p "Analyze these commits and suggest a release note"

# コードレビュー支援
git diff HEAD~1 | copilot -p "Review this code change and suggest improvements"
```

## ? 自動化とスクリプト統合

### Bashスクリプトでの使用
```bash
#!/bin/bash
# deploy-helper.sh

LOG_FILE="/var/log/deploy.log"
ANALYSIS=$(tail -50 "$LOG_FILE" | copilot -p "Check if deployment was successful and summarize any issues")

echo "Deployment Analysis:"
echo "$ANALYSIS"

# 結果に基づいて条件分岐
if echo "$ANALYSIS" | grep -qi "error\|failed"; then
    echo "Deployment issues detected. Please review."
    exit 1
fi
```

### CI/CDでの使用例
```yaml
# .github/workflows/code-review.yml
name: AI Code Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  ai-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Install Copilot CLI
        run: npm install -g @github/copilot
        
      - name: AI Code Review
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          git diff origin/main...HEAD | copilot -p "Review this code change for potential issues and improvements" > review.md
          gh pr comment --body-file review.md
```

### Makefileでの使用
```makefile
# Makefile
.PHONY: analyze-logs
analyze-logs:
	@tail -100 app.log | copilot -p "Analyze these logs and identify any concerning patterns"

.PHONY: generate-docs
generate-docs:
	@find src -name "*.js" -exec cat {} \; | copilot -p "Generate API documentation for these JavaScript files"
```

## ?? 利用可能なオプション

現在確認されているオプション：
- **`-p, --prompt`**: プロンプトを指定（必須）
- プロンプトに `-` を指定すると標準入力から読み込み

## ? 実践的な活用シナリオ

### 開発ワークフロー統合
```bash
# 1. コミット前の自動チェック
git diff --cached | copilot -p "Review staged changes for potential issues"

# 2. テスト結果の分析
npm test 2>&1 | copilot -p "Analyze test results and suggest fixes for failures"

# 3. パフォーマンス分析
curl -w "@curl-format.txt" -s https://api.example.com/health | copilot -p "Analyze API performance metrics"
```

### システム管理
```bash
# 1. システムヘルス チェック
df -h && free -h && uptime | copilot -p "Analyze system health and recommend actions"

# 2. セキュリティログ分析
journalctl -u ssh.service --since "1 hour ago" | copilot -p "Check for suspicious SSH activity"

# 3. プロセス分析
ps aux --sort=-%cpu | head -20 | copilot -p "Identify resource-intensive processes and optimization opportunities"
```

## ?? 注意事項と制限

### 認証要件
- GitHub Copilot サブスクリプションが必要
- 事前に `copilot` でログインしておく必要がある
- 環境変数 `GH_TOKEN` または `GITHUB_TOKEN` での認証も可能

### セキュリティ考慮事項
- 機密情報を含むデータをプロンプトに含めないよう注意
- 本番環境でのスクリプト実行前に十分なテスト実施を推奨
- 生成されたコマンドは実行前に必ず確認する

### パフォーマンス
- 各プロンプトでプレミアムリクエストクォータを1つ消費
- 大量のデータを一度に送信すると応答時間が長くなる可能性
- バッチ処理では適切な間隔を設けることを推奨

## ? 今後の展望

GitHub issue #96のディスカッションによると、将来的には以下の機能が検討されています：

### 予想される将来の機能
- **`copilot exec`** サブコマンド
- **`--model`** オプション（モデル選択）
- **`--json`** オプション（JSON出力）
- **`--full-auto`** オプション（自動実行）
- **`--sandbox`** オプション（サンドボックス実行）

### 現在の状況
- 基本的な `-p` オプションは v0.0.329 以前から利用可能
- エラーメッセージは v0.0.329 で改善済み
- より詳細なオプションは将来のリリースで追加予定

## ? まとめ

GitHub Copilot CLIのプログラムモードを使用することで：

1. **スクリプト自動化**: Bash、Python、CI/CDパイプラインでの統合
2. **ログ分析**: リアルタイムまたはバッチでのログファイル解析
3. **コード生成**: 繰り返し作業の自動化
4. **システム監視**: ヘルスチェックや パフォーマンス分析

この機能により、GitHub Copilot CLIをインタラクティブな開発支援ツールとしてだけでなく、DevOpsやシステム管理の自動化ツールとしても活用できます。

---

**注意**: この機能はパブリックプレビュー段階のため、将来的に変更される可能性があります。最新の情報については公式ドキュメントを確認してください。