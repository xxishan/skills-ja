---
name: webapp-testing
description: Playwrightを使用してローカルWebアプリケーションとの対話およびテストを行うツールキット。フロントエンド機能の検証、UIの動作デバッグ、ブラウザスクリーンショットの取得、ブラウザログの表示をサポートします。
license: 完全な条項はLICENSE.txtに記載
---

# Web アプリケーションテスト

ローカル Web アプリケーションをテストするには、ネイティブ Python Playwright スクリプトを記述します。

**利用可能なヘルパースクリプト**:

- `scripts/with_server.py` - サーバーのライフサイクルを管理（複数サーバーをサポート）

**使用方法を確認するために、常に最初に `--help` を付けてスクリプトを実行してください**。カスタマイズされたソリューションが絶対に必要であることが判明するまで、ソースコードは読まないでください。これらのスクリプトは非常に大きくなる可能性があり、コンテキストウィンドウを汚染します。これらはコンテキストウィンドウに取り込むのではなく、ブラックボックススクリプトとして直接呼び出すために存在します。

## 決定木：アプローチの選択

```
ユーザータスク → 静的HTMLか？
    ├─ はい → セレクタを特定するためにHTMLファイルを直接読み込む
    │         ├─ 成功 → セレクタを使用してPlaywrightスクリプトを記述
    │         └─ 失敗/不完全 → 動的として扱う（以下）
    │
    └─ いいえ（動的Webアプリ） → サーバーは既に実行中か？
        ├─ いいえ → 実行: python scripts/with_server.py --help
        │        その後、ヘルパーを使用 + 簡略化されたPlaywrightスクリプトを記述
        │
        └─ はい → 偵察してから行動：
            1. ナビゲートしてnetworkidleを待つ
            2. スクリーンショットを撮るかDOMを検査
            3. レンダリングされた状態からセレクタを特定
            4. 発見したセレクタを使用してアクションを実行
```

## 例：with_server.py の使用

サーバーを起動するには、まず `--help` を実行してから、ヘルパーを使用します：

**単一サーバー：**

```bash
python scripts/with_server.py --server "npm run dev" --port 5173 -- python your_automation.py
```

**複数サーバー（例：バックエンド + フロントエンド）：**

```bash
python scripts/with_server.py \
  --server "cd backend && python server.py" --port 3000 \
  --server "cd frontend && npm run dev" --port 5173 \
  -- python your_automation.py
```

自動化スクリプトを作成するには、Playwright のロジックのみを含めます（サーバーは自動的に管理されます）：

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch(headless=True) # 常にヘッドレスモードでchromiumを起動
    page = browser.new_page()
    page.goto('http://localhost:5173') # サーバーは既に実行中で準備完了
    page.wait_for_load_state('networkidle') # 重要：JSの実行を待つ
    # ... あなたの自動化ロジック
    browser.close()
```

## 偵察してから行動パターン

1. **レンダリングされた DOM を検査**：

   ```python
   page.screenshot(path='/tmp/inspect.png', full_page=True)
   content = page.content()
   page.locator('button').all()
   ```

2. 検査結果から**セレクタを特定**

3. 発見したセレクタを使用して**アクションを実行**

## よくある落とし穴

❌ **してはいけないこと** 動的アプリで `networkidle` を待つ前に DOM を検査する
✅ **すべきこと** 検査の前に `page.wait_for_load_state('networkidle')` を待つ

## ベストプラクティス

- **バンドルされたスクリプトをブラックボックスとして使用** - タスクを達成するために、`scripts/` で利用可能なスクリプトの 1 つが役立つかどうかを検討してください。これらのスクリプトは、コンテキストウィンドウを汚染することなく、一般的で複雑なワークフローを確実に処理します。`--help` を使用して使い方を確認してから、直接呼び出してください。
- 同期スクリプトには `sync_playwright()` を使用
- 完了したら必ずブラウザを閉じる
- 説明的なセレクタを使用：`text=`、`role=`、CSS セレクタ、または ID
- 適切な待機を追加：`page.wait_for_selector()` または `page.wait_for_timeout()`

## リファレンスファイル

- **examples/** - 一般的なパターンを示す例：
  - `element_discovery.py` - ページ上のボタン、リンク、入力要素を発見
  - `static_html_automation.py` - ローカル HTML のための file:// URL の使用
  - `console_logging.py` - 自動化中のコンソールログのキャプチャ
