# MCP サーバー評価ガイド

## 概要

このドキュメントは、MCP サーバーの包括的な評価を作成するためのガイダンスを提供します。評価は、LLM が提供されたツールのみを使用して、現実的で複雑な質問に効果的に答えるために MCP サーバーを使用できるかをテストします。

---

## クイックリファレンス

### 評価要件

- 10 個の人間が読める質問を作成
- 質問は読み取り専用、独立、非破壊的でなければならない
- 各質問は複数のツール呼び出し（潜在的に数十回）を必要とする
- 回答は単一の検証可能な値でなければならない
- 回答は安定している必要がある（時間とともに変化しない）

### 出力フォーマット

```xml
<evaluation>
   <qa_pair>
      <question>ここに質問</question>
      <answer>単一の検証可能な回答</answer>
   </qa_pair>
</evaluation>
```

---

## 評価の目的

MCP サーバーの品質の尺度は、サーバーがツールをどれだけうまく包括的に実装しているかではなく、これらの実装（入出力スキーマ、docstring/説明、機能）が、他のコンテキストなしで MCP サーバーのみにアクセスできる LLM が現実的で困難な質問に答えることをどれだけうまく可能にするかです。

## 評価の概要

読み取り専用、独立、非破壊的、かつ冪等な操作のみを必要とする 10 個の人間が読める質問を作成します。各質問は以下であるべきです:

- 現実的
- 明確で簡潔
- 曖昧でない
- 複雑で、潜在的に数十のツール呼び出しまたはステップを必要とする
- 事前に特定した単一の検証可能な値で答えられる

## 質問のガイドライン

### コア要件

1. **質問は独立していなければならない**

   - 各質問は他の質問の回答に依存してはならない
   - 別の質問を処理することによる事前の書き込み操作を想定してはならない

2. **質問は非破壊的かつ冪等なツール使用のみを必要としなければならない**

   - 正しい回答に到達するために状態を変更することを指示または要求してはならない

3. **質問は現実的、明確、簡潔、かつ複雑でなければならない**
   - 別の LLM が答えるために複数の（潜在的に数十の）ツールまたはステップを使用する必要がある

### 複雑さと深さ

4. **質問は深い探索を必要としなければならない**

   - 複数のサブ質問と連続したツール呼び出しを必要とするマルチホップ質問を検討する
   - 各ステップは前の質問で見つかった情報から利益を得るべき

5. **質問は広範なページングを必要とする場合がある**

   - 結果の複数ページをページングする必要がある場合がある
   - ニッチな情報を見つけるために古いデータ（1〜2 年前）をクエリする必要がある場合がある
   - 質問は困難でなければならない

6. **質問は深い理解を必要としなければならない**

   - 表面的な知識ではなく
   - 証拠を必要とする複雑なアイデアを真偽質問として提示する場合がある
   - LLM が異なる仮説を検索しなければならない多肢選択形式を使用する場合がある

7. **質問は単純なキーワード検索で解決できてはならない**
   - ターゲットコンテンツの特定のキーワードを含めない
   - 同義語、関連する概念、または言い換えを使用する
   - 複数の検索、複数の関連項目の分析、コンテキストの抽出、その後回答の導出を必要とする

### ツールテスト

8. **質問はツールの戻り値をストレステストするべき**

   - LLM を圧倒する大きな JSON オブジェクトまたはリストを返すツールを引き出す場合がある
   - データの複数のモダリティを理解する必要があるべき:
     - ID と名前
     - タイムスタンプと日時（月、日、年、秒）
     - ファイル ID、名前、拡張子、およびマイムタイプ
     - URL、GID など
   - ツールがすべての有用な形式のデータを返す能力を調査するべき

9. **質問は主に実際の人間のユースケースを反映するべき**

   - LLM の支援を受けた人間が気にするような情報検索タスクの種類

10. **質問は数十のツール呼び出しを必要とする場合がある**

    - これは限られたコンテキストを持つ LLM に挑戦する
    - MCP サーバーツールが返される情報を減らすことを奨励する

11. **曖昧な質問を含める**
    - 曖昧である、またはどのツールを呼び出すかについて難しい決定を必要とする場合がある
    - LLM が潜在的に間違いを犯したり誤解したりすることを強制する
    - 曖昧さにもかかわらず、単一の検証可能な回答がまだ存在することを確認する

### 安定性

12. **質問は回答が変化しないように設計されなければならない**

    - 動的な「現在の状態」に依存する質問をしない
    - 例えば、以下をカウントしない:
      - 投稿へのリアクションの数
      - スレッドへの返信の数
      - チャンネルのメンバーの数

13. **MCP サーバーが作成する質問の種類を制限させない**
    - 挑戦的で複雑な質問を作成する
    - 利用可能な MCP サーバーツールでは解決できないものもある場合がある
    - 質問は特定の出力フォーマットを必要とする場合がある（日時対エポックタイム、JSON 対 MARKDOWN）
    - 質問は完了するために数十のツール呼び出しを必要とする場合がある

## 回答のガイドライン

### 検証

1. **回答は直接文字列比較で検証可能でなければならない**
   - 回答が多くのフォーマットで書き直せる場合、質問で出力フォーマットを明確に指定する
   - 例: "YYYY/MM/DD を使用してください。"、"真または偽で応答してください。"、"A、B、C、または D のみで答えてください。"
   - 回答は以下のような単一の検証可能な値であるべき:
     - ユーザー ID、ユーザー名、表示名、名、姓
     - チャンネル ID、チャンネル名
     - メッセージ ID、文字列
     - URL、タイトル
     - 数値
     - タイムスタンプ、日時
     - ブール値（真偽質問用）
     - メールアドレス、電話番号
     - ファイル ID、ファイル名、ファイル拡張子
     - 多肢選択の回答
   - 回答は特別なフォーマットや複雑で構造化された出力を必要としてはならない
   - 回答は直接文字列比較を使用して検証される

### 可読性

2. **回答は一般的に人間が読める形式を優先すべき**
   - 例: 名前、名、姓、日時、ファイル名、メッセージ文字列、URL、はい/いいえ、真/偽、a/b/c/d
   - 不透明な ID ではなく（ただし ID も許容される）
   - 大多数の回答は人間が読めるべき

### 安定性

3. **回答は安定/定常でなければならない**

   - 古いコンテンツを見る（例: 終了した会話、開始されたプロジェクト、回答された質問）
   - 常に同じ回答を返す「閉じた」概念に基づいて質問を作成する
   - 質問は非定常な回答から隔離するために固定時間ウィンドウを考慮するよう求める場合がある
   - 変化する可能性が低いコンテキストに依存する
   - 例: 論文名を見つける場合、後に公開される論文と混同されないように十分に具体的にする

4. **回答は明確で曖昧でなければならない**
   - 質問は単一の明確な回答が存在するように設計されなければならない
   - 回答は MCP サーバーツールを使用して導出できる

### 多様性

5. **回答は多様でなければならない**

   - 回答は多様なモダリティとフォーマットの単一の検証可能な値であるべき
   - ユーザー概念: ユーザー ID、ユーザー名、表示名、名、姓、メールアドレス、電話番号
   - チャンネル概念: チャンネル ID、チャンネル名、チャンネルトピック
   - メッセージ概念: メッセージ ID、メッセージ文字列、タイムスタンプ、月、日、年

6. **回答は複雑な構造であってはならない**
   - 値のリストではない
   - 複雑なオブジェクトではない
   - ID や文字列のリストではない
   - 自然言語テキストではない
   - 直接文字列比較を使用して直接検証できる場合を除く
   - そして現実的に再現できる
   - LLM が同じリストを他の順序やフォーマットで返す可能性は低いべき

## 評価プロセス

### ステップ 1: ドキュメント検査

ターゲット API のドキュメントを読んで理解する:

- 利用可能なエンドポイントと機能
- 曖昧さが存在する場合、Web から追加情報を取得
- このステップを可能な限り並列化する
- 各サブエージェントがファイルシステムまたは Web 上のドキュメントのみを検査していることを確認

### ステップ 2: ツール検査

MCP サーバーで利用可能なツールをリストアップ:

- MCP サーバーを直接検査
- 入出力スキーマ、docstring、説明を理解
- この段階ではツール自体を呼び出さない

### ステップ 3: 理解の発展

十分な理解が得られるまでステップ 1 と 2 を繰り返す:

- 複数回反復する
- 作成したいタスクの種類について考える
- 理解を深める
- いかなる段階でも MCP サーバー実装自体のコードを読んではならない
- 直感と理解を使って、合理的で現実的だが非常に挑戦的なタスクを作成

### ステップ 4: 読み取り専用コンテンツ検査

API とツールを理解した後、MCP サーバーツールを使用する:

- 読み取り専用および非破壊的な操作のみを使用してコンテンツを検査
- 目標: 現実的な質問を作成するための特定のコンテンツ（例: ユーザー、チャンネル、メッセージ、プロジェクト、タスク）を識別する
- 状態を変更するツールを呼び出してはならない
- MCP サーバー実装のコードを読まない
- 独立した探索を追求する個々のサブエージェントでこのステップを並列化
- 各サブエージェントが読み取り専用、非破壊的、冪等な操作のみを実行していることを確認
- 注意: 一部のツールは大量のデータを返し、コンテキストが不足する可能性がある
- 探索のために段階的、小規模、ターゲット化されたツール呼び出しを行う
- すべてのツール呼び出しリクエストで、`limit`パラメータを使用して結果を制限する（<10）
- ページネーションを使用

### ステップ 5: タスク生成

コンテンツを検査した後、10 個の人間が読める質問を作成する:

- LLM が MCP サーバーでこれらに答えられるべき
- 上記のすべての質問と回答のガイドラインに従う

## 出力フォーマット

各 QA ペアは質問と回答で構成されます。出力は以下の構造を持つ XML ファイルであるべきです:

```xml
<evaluation>
   <qa_pair>
      <question>2024年Q2に作成され、完了したタスクの数が最も多いプロジェクトを見つけてください。プロジェクト名は何ですか？</question>
      <answer>Website Redesign</answer>
   </qa_pair>
   <qa_pair>
      <question>「バグ」とラベル付けされ、2024年3月にクローズされた問題を検索してください。最も多くの問題をクローズしたユーザーは誰ですか？ユーザー名を提供してください。</question>
      <answer>sarah_dev</answer>
   </qa_pair>
   <qa_pair>
      <question>/apiディレクトリのファイルを変更し、2024年1月1日から1月31日の間にマージされたプルリクエストを探してください。これらのPRに取り組んだ異なるコントリビューターは何人いましたか？</question>
      <answer>7</answer>
   </qa_pair>
   <qa_pair>
      <question>2023年より前に作成され、最も多くのスターを持つリポジトリを見つけてください。リポジトリ名は何ですか？</question>
      <answer>data-pipeline</answer>
   </qa_pair>
</evaluation>
```

## 評価の例

### 良い質問

**例 1: 深い探索を必要とするマルチホップ質問（GitHub MCP）**

```xml
<qa_pair>
   <question>2023年Q3にアーカイブされ、以前は組織内で最もフォークされたプロジェクトだったリポジトリを見つけてください。そのリポジトリで使用されていた主要なプログラミング言語は何でしたか？</question>
   <answer>Python</answer>
</qa_pair>
```

この質問が良い理由:

- アーカイブされたリポジトリを見つけるために複数の検索が必要
- アーカイブ前に最も多くフォークされたものを識別する必要がある
- 言語のためにリポジトリの詳細を調べる必要がある
- 回答はシンプルで検証可能な値
- 変化しない過去の（クローズされた）データに基づいている

**例 2: キーワードマッチングなしでコンテキストの理解が必要（プロジェクト管理 MCP）**

```xml
<qa_pair>
   <question>2023年後半に完了した顧客オンボーディングの改善に焦点を当てたイニシアチブを見つけてください。プロジェクトリーダーは完了後に振り返りドキュメントを作成しました。その時点でのリーダーの役職は何でしたか？</question>
   <answer>Product Manager</answer>
</qa_pair>
```

この質問が良い理由:

- 特定のプロジェクト名を使用していない（「顧客オンボーディングの改善に焦点を当てたイニシアチブ」）
- 特定の時間枠から完了したプロジェクトを見つける必要がある
- プロジェクトリーダーとその役割を識別する必要がある
- 振り返りドキュメントからのコンテキストの理解が必要
- 回答は人間が読めて安定している
- 完了した作業に基づいている（変化しない）

**例 3: 複数のステップを必要とする複雑な集約（問題トラッカー MCP）**

```xml
<qa_pair>
   <question>2024年1月に報告され、クリティカル優先度としてマークされたすべてのバグの中で、48時間以内に割り当てられたバグの最も高い割合を解決した担当者は誰ですか？担当者のユーザー名を提供してください。</question>
   <answer>alex_eng</answer>
</qa_pair>
```

この質問が良い理由:

- 日付、優先度、ステータスでバグをフィルタリングする必要がある
- 担当者ごとにグループ化し、解決率を計算する必要がある
- 48 時間のウィンドウを決定するためにタイムスタンプを理解する必要がある
- ページネーションをテスト（処理するバグが潜在的に多数）
- 回答は単一のユーザー名
- 特定の期間の過去データに基づいている

**例 4: 複数のデータタイプにわたる統合が必要（CRM MCP）**

```xml
<qa_pair>
   <question>2023年Q4にStarterからEnterpriseプランにアップグレードし、年間契約額が最も高かったアカウントを見つけてください。このアカウントはどの業界で運営していますか？</question>
   <answer>Healthcare</answer>
</qa_pair>
```

この質問が良い理由:

- サブスクリプション層の変更を理解する必要がある
- 特定の時間枠でアップグレードイベントを識別する必要がある
- 契約額を比較する必要がある
- アカウントの業界情報にアクセスする必要がある
- 回答はシンプルで検証可能
- 完了した過去のトランザクションに基づいている

### 悪い質問

**例 1: 回答が時間とともに変化する**

```xml
<qa_pair>
   <question>現在エンジニアリングチームに割り当てられているオープンな問題はいくつありますか？</question>
   <answer>47</answer>
</qa_pair>
```

この質問が悪い理由:

- 問題が作成、クローズ、または再割り当てされると回答が変化する
- 安定/定常データに基づいていない
- 動的な「現在の状態」に依存している

**例 2: キーワード検索で簡単すぎる**

```xml
<qa_pair>
   <question>「認証機能の追加」というタイトルのプルリクエストを見つけて、誰が作成したか教えてください。</question>
   <answer>developer123</answer>
</qa_pair>
```

この質問が悪い理由:

- 正確なタイトルの単純なキーワード検索で解決できる
- 深い探索や理解を必要としない
- 統合や分析が不要

**例 3: 曖昧な回答フォーマット**

```xml
<qa_pair>
   <question>Pythonを主要言語として持つすべてのリポジトリをリストアップしてください。</question>
   <answer>repo1, repo2, repo3, data-pipeline, ml-tools</answer>
</qa_pair>
```

この質問が悪い理由:

- 回答は任意の順序で返される可能性があるリスト
- 直接文字列比較での検証が困難
- LLM が異なる形式にする可能性がある（JSON 配列、カンマ区切り、改行区切り）
- 特定の集計（カウント）または最上級（最も多くのスター）を尋ねる方が良い

## 検証プロセス

評価を作成した後:

1. **XML ファイルを検査**してスキーマを理解する
2. **各タスク指示をロード**し、MCP サーバーとツールを使用して並列に、自分でタスクを解こうとして正しい回答を識別する
3. **書き込みまたは破壊的操作を必要とする操作をフラグ付け**する
4. **すべての正しい回答を蓄積**し、ドキュメント内の誤った回答を置き換える
5. **書き込みまたは破壊的操作を必要とする`<qa_pair>`を削除**する

コンテキストが不足しないようにタスクの解決を並列化し、最後にすべての回答を蓄積してファイルに変更を加えることを忘れないでください。

## 質の高い評価を作成するためのヒント

1. **タスクを生成する前に深く考え、事前に計画する**
2. **機会があれば並列化する** プロセスを高速化し、コンテキストを管理する
3. **現実的なユースケースに焦点を当てる** 人間が実際に達成したいこと
4. **挑戦的な質問を作成する** MCP サーバーの能力の限界をテストする
5. **安定性を確保する** 過去のデータとクローズされた概念を使用する
6. **回答を検証する** MCP サーバーツールを使用して自分で質問を解く
7. **反復して改善する** プロセス中に学んだことに基づいて

---

# 評価の実行

評価ファイルを作成した後、提供された評価ハーネスを使用して MCP サーバーをテストできます。

## セットアップ

1. **依存関係をインストール**

   ```bash
   pip install -r scripts/requirements.txt
   ```

   または手動でインストール:

   ```bash
   pip install anthropic mcp
   ```

2. **API キーを設定**

   ```bash
   export ANTHROPIC_API_KEY=your_api_key_here
   ```

## 評価ファイル形式

評価ファイルは`<qa_pair>`要素を持つ XML 形式を使用します:

```xml
<evaluation>
   <qa_pair>
      <question>2024年Q2に作成され、完了したタスクの数が最も多いプロジェクトを見つけてください。プロジェクト名は何ですか？</question>
      <answer>Website Redesign</answer>
   </qa_pair>
   <qa_pair>
      <question>「バグ」とラベル付けされ、2024年3月にクローズされた問題を検索してください。最も多くの問題をクローズしたユーザーは誰ですか？ユーザー名を提供してください。</question>
      <answer>sarah_dev</answer>
   </qa_pair>
</evaluation>
```

## 評価の実行

評価スクリプト（`scripts/evaluation.py`）は 3 つのトランスポートタイプをサポートしています:

**重要:**

- **stdio トランスポート**: 評価スクリプトが自動的に MCP サーバープロセスを起動して管理します。サーバーを手動で実行しないでください。
- **sse/http トランスポート**: 評価を実行する前に MCP サーバーを個別に起動する必要があります。スクリプトは指定された URL で既に実行中のサーバーに接続します。

### 1. ローカル STDIO サーバー

ローカルで実行される MCP サーバーの場合（スクリプトが自動的にサーバーを起動）:

```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a my_mcp_server.py \
  evaluation.xml
```

環境変数を使用:

```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a my_mcp_server.py \
  -e API_KEY=abc123 \
  -e DEBUG=true \
  evaluation.xml
```

### 2. Server-Sent Events（SSE）

SSE ベースの MCP サーバーの場合（最初にサーバーを起動する必要があります）:

```bash
python scripts/evaluation.py \
  -t sse \
  -u https://example.com/mcp \
  -H "Authorization: Bearer token123" \
  -H "X-Custom-Header: value" \
  evaluation.xml
```

### 3. HTTP（ストリーマブル HTTP）

HTTP ベースの MCP サーバーの場合（最初にサーバーを起動する必要があります）:

```bash
python scripts/evaluation.py \
  -t http \
  -u https://example.com/mcp \
  -H "Authorization: Bearer token123" \
  evaluation.xml
```

## コマンドラインオプション

```
使用法: evaluation.py [-h] [-t {stdio,sse,http}] [-m MODEL] [-c COMMAND]
                     [-a ARGS [ARGS ...]] [-e ENV [ENV ...]] [-u URL]
                     [-H HEADERS [HEADERS ...]] [-o OUTPUT]
                     eval_file

位置引数:
  eval_file             評価XMLファイルへのパス

オプション引数:
  -h, --help            ヘルプメッセージを表示
  -t, --transport       トランスポートタイプ: stdio、sse、またはhttp（デフォルト: stdio）
  -m, --model           使用するClaudeモデル（デフォルト: claude-3-7-sonnet-20250219）
  -o, --output          レポートの出力ファイル（デフォルト: stdoutに出力）

stdioオプション:
  -c, --command         MCPサーバーを実行するコマンド（例: python、node）
  -a, --args            コマンドの引数（例: server.py）
  -e, --env             KEY=VALUE形式の環境変数

sse/httpオプション:
  -u, --url             MCPサーバーURL
  -H, --header          'Key: Value'形式のHTTPヘッダー
```

## 出力

評価スクリプトは以下を含む詳細なレポートを生成します:

- **要約統計**:

  - 精度（正解数/総数）
  - タスクあたりの平均所要時間
  - タスクあたりの平均ツール呼び出し数
  - 総ツール呼び出し数

- **タスクごとの結果**:
  - プロンプトと期待される応答
  - エージェントからの実際の応答
  - 回答が正しかったか（✅/❌）
  - 所要時間とツール呼び出しの詳細
  - エージェントのアプローチの要約
  - ツールに関するエージェントのフィードバック

### レポートをファイルに保存

```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a my_server.py \
  -o evaluation_report.md \
  evaluation.xml
```

## 完全な例のワークフロー

評価の作成と実行の完全な例は以下の通りです:

1. **評価ファイルを作成**（`my_evaluation.xml`）:

```xml
<evaluation>
   <qa_pair>
      <question>2024年1月に最も多くの問題を作成したユーザーを見つけてください。彼らのユーザー名は何ですか？</question>
      <answer>alice_developer</answer>
   </qa_pair>
   <qa_pair>
      <question>2024年Q1にマージされたすべてのプルリクエストの中で、最も多くの数を持つリポジトリはどれですか？リポジトリ名を提供してください。</question>
      <answer>backend-api</answer>
   </qa_pair>
   <qa_pair>
      <question>2023年12月に完了し、開始から終了までの期間が最も長かったプロジェクトを見つけてください。何日かかりましたか？</question>
      <answer>127</answer>
   </qa_pair>
</evaluation>
```

2. **依存関係をインストール**:

```bash
pip install -r scripts/requirements.txt
export ANTHROPIC_API_KEY=your_api_key
```

3. **評価を実行**:

```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a github_mcp_server.py \
  -e GITHUB_TOKEN=ghp_xxx \
  -o github_eval_report.md \
  my_evaluation.xml
```

4. **`github_eval_report.md`でレポートを確認**して:
   - どの質問が合格/不合格だったかを確認
   - ツールに関するエージェントのフィードバックを読む
   - 改善領域を特定
   - MCP サーバー設計を反復

## トラブルシューティング

### 接続エラー

接続エラーが発生した場合:

- **STDIO**: コマンドと引数が正しいことを確認
- **SSE/HTTP**: URL がアクセス可能で、ヘッダーが正しいことを確認
- 必要な API キーが環境変数またはヘッダーに設定されていることを確認

### 低い精度

多くの評価が失敗する場合:

- 各タスクのエージェントのフィードバックを確認
- ツールの説明が明確で包括的かチェック
- 入力パラメータが十分にドキュメント化されているか確認
- ツールが返すデータが多すぎるか少なすぎるか検討
- エラーメッセージが実行可能であることを確認

### タイムアウトの問題

タスクがタイムアウトする場合:

- より強力なモデルを使用（例: `claude-3-7-sonnet-20250219`）
- ツールが返すデータが多すぎないかチェック
- ページネーションが正しく機能しているか確認
- 複雑な質問を簡素化することを検討

## 出力フォーマット

各 QA ペアは質問と回答で構成されます。出力は以下の構造を持つ XML ファイルであるべきです:

```xml
<evaluation>
   <qa_pair>
      <question>2024年Q2に作成され、完了したタスクの数が最も多いプロジェクトを見つけてください。プロジェクト名は何ですか？</question>
      <answer>Website Redesign</answer>
   </qa_pair>
   <qa_pair>
      <question>「バグ」とラベル付けされ、2024年3月にクローズされた問題を検索してください。最も多くの問題をクローズしたユーザーは誰ですか？ユーザー名を提供してください。</question>
      <answer>sarah_dev</answer>
   </qa_pair>
   <qa_pair>
      <question>/apiディレクトリのファイルを変更し、2024年1月1日から1月31日の間にマージされたプルリクエストを探してください。これらのPRに取り組んだ異なるコントリビューターは何人いますか？</question>
      <answer>7</answer>
   </qa_pair>
   <qa_pair>
      <question>2023年より前に作成され、最も多くのスターを持つリポジトリを見つけてください。リポジトリ名は何ですか？</question>
      <answer>data-pipeline</answer>
   </qa_pair>
</evaluation>
```

## 評価の例

### 良い質問

**例 1: 深い探索を必要とするマルチホップ質問（GitHub MCP）**

```xml
<qa_pair>
   <question>2023年Q3にアーカイブされ、以前は組織内で最もフォークされたプロジェクトだったリポジトリを見つけてください。そのリポジトリで使用されていた主要なプログラミング言語は何でしたか？</question>
   <answer>Python</answer>
</qa_pair>
```

この質問が良い理由:

- アーカイブされたリポジトリを見つけるために複数の検索が必要
- アーカイブ前に最も多くフォークされたものを識別する必要がある
- 言語のためにリポジトリの詳細を調べる必要がある
- 回答はシンプルで検証可能な値
- 変化しない過去の（クローズされた）データに基づいている

**例 2: キーワードマッチングなしでコンテキストの理解が必要（プロジェクト管理 MCP）**

```xml
<qa_pair>
   <question>2023年後半に完了した顧客オンボーディングの改善に焦点を当てたイニシアチブを見つけてください。プロジェクトリーダーは完了後に振り返りドキュメントを作成しました。その時点でのリーダーの役職は何でしたか？</question>
   <answer>Product Manager</answer>
</qa_pair>
```

この質問が良い理由:

- 特定のプロジェクト名を使用していない（「顧客オンボーディングの改善に焦点を当てたイニシアチブ」）
- 特定の時間枠から完了したプロジェクトを見つける必要がある
- プロジェクトリーダーとその役割を識別する必要がある
- 振り返りドキュメントからのコンテキストの理解が必要
- 回答は人間が読めて安定している
- 完了した作業に基づいている（変化しない）

**例 3: 複数のステップを必要とする複雑な集約（問題トラッカー MCP）**

```xml
<qa_pair>
   <question>2024年1月に報告され、クリティカル優先度としてマークされたすべてのバグの中で、48時間以内に割り当てられたバグの最も高い割合を解決した担当者は誰ですか？担当者のユーザー名を提供してください。</question>
   <answer>alex_eng</answer>
</qa_pair>
```

この質問が良い理由:

- 日付、優先度、ステータスでバグをフィルタリングする必要がある
- 担当者ごとにグループ化し、解決率を計算する必要がある
- 48 時間のウィンドウを決定するためにタイムスタンプを理解する必要がある
- ページネーションをテスト（処理するバグが潜在的に多数）
- 回答は単一のユーザー名
- 特定の期間の過去データに基づいている

**例 4: 複数のデータタイプにわたる統合が必要（CRM MCP）**

```xml
<qa_pair>
   <question>2023年Q4にStarterからEnterpriseプランにアップグレードし、年間契約額が最も高かったアカウントを見つけてください。このアカウントはどの業界で運営していますか？</question>
   <answer>Healthcare</answer>
</qa_pair>
```

この質問が良い理由:

- サブスクリプション層の変更を理解する必要がある
- 特定の時間枠でアップグレードイベントを識別する必要がある
- 契約額を比較する必要がある
- アカウントの業界情報にアクセスする必要がある
- 回答はシンプルで検証可能
- 完了した過去のトランザクションに基づいている

### 悪い質問

**例 1: 回答が時間とともに変化する**

```xml
<qa_pair>
   <question>現在エンジニアリングチームに割り当てられているオープンな問題はいくつありますか？</question>
   <answer>47</answer>
</qa_pair>
```

この質問が悪い理由:

- 問題が作成、クローズ、または再割り当てされると回答が変化する
- 安定/定常データに基づいていない
- 動的な「現在の状態」に依存している

**例 2: キーワード検索で簡単すぎる**

```xml
<qa_pair>
   <question>「認証機能の追加」というタイトルのプルリクエストを見つけて、誰が作成したか教えてください。</question>
   <answer>developer123</answer>
</qa_pair>
```

この質問が悪い理由:

- 正確なタイトルの単純なキーワード検索で解決できる
- 深い探索や理解を必要としない
- 統合や分析が不要

**例 3: 曖昧な回答フォーマット**

```xml
<qa_pair>
   <question>Pythonを主要言語として持つすべてのリポジトリをリストアップしてください。</question>
   <answer>repo1, repo2, repo3, data-pipeline, ml-tools</answer>
</qa_pair>
```

この質問が悪い理由:

- 回答は任意の順序で返される可能性があるリスト
- 直接文字列比較での検証が困難
- LLM が異なる形式にする可能性がある（JSON 配列、カンマ区切り、改行区切り）
- 特定の集計（カウント）または最上級（最も多くのスター）を尋ねる方が良い

## Verification Process

After creating evaluations:

1. **Examine the XML file** to understand the schema
2. **Load each task instruction** and in parallel using the MCP server and tools, identify the correct answer by attempting to solve the task YOURSELF
3. **Flag any operations** that require WRITE or DESTRUCTIVE operations
4. **Accumulate all CORRECT answers** and replace any incorrect answers in the document
5. **Remove any `<qa_pair>`** that require WRITE or DESTRUCTIVE operations

Remember to parallelize solving tasks to avoid running out of context, then accumulate all answers and make changes to the file at the end.

## Tips for Creating Quality Evaluations

1. **Think Hard and Plan Ahead** before generating tasks
2. **Parallelize Where Opportunity Arises** to speed up the process and manage context
3. **Focus on Realistic Use Cases** that humans would actually want to accomplish
4. **Create Challenging Questions** that test the limits of the MCP server's capabilities
5. **Ensure Stability** by using historical data and closed concepts
6. **Verify Answers** by solving the questions yourself using the MCP server tools
7. **Iterate and Refine** based on what you learn during the process

---

# Running Evaluations

After creating your evaluation file, you can use the provided evaluation harness to test your MCP server.

## Setup

1. **Install Dependencies**

   ```bash
   pip install -r scripts/requirements.txt
   ```

   Or install manually:

   ```bash
   pip install anthropic mcp
   ```

2. **Set API Key**

   ```bash
   export ANTHROPIC_API_KEY=your_api_key_here
   ```

## Evaluation File Format

Evaluation files use XML format with `<qa_pair>` elements:

```xml
<evaluation>
   <qa_pair>
      <question>Find the project created in Q2 2024 with the highest number of completed tasks. What is the project name?</question>
      <answer>Website Redesign</answer>
   </qa_pair>
   <qa_pair>
      <question>Search for issues labeled as "bug" that were closed in March 2024. Which user closed the most issues? Provide their username.</question>
      <answer>sarah_dev</answer>
   </qa_pair>
</evaluation>
```

## Running Evaluations

The evaluation script (`scripts/evaluation.py`) supports three transport types:

**Important:**

- **stdio transport**: The evaluation script automatically launches and manages the MCP server process for you. Do not run the server manually.
- **sse/http transports**: You must start the MCP server separately before running the evaluation. The script connects to the already-running server at the specified URL.

### 1. Local STDIO Server

For locally-run MCP servers (script launches the server automatically):

```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a my_mcp_server.py \
  evaluation.xml
```

With environment variables:

```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a my_mcp_server.py \
  -e API_KEY=abc123 \
  -e DEBUG=true \
  evaluation.xml
```

### 2. Server-Sent Events (SSE)

For SSE-based MCP servers (you must start the server first):

```bash
python scripts/evaluation.py \
  -t sse \
  -u https://example.com/mcp \
  -H "Authorization: Bearer token123" \
  -H "X-Custom-Header: value" \
  evaluation.xml
```

### 3. HTTP (Streamable HTTP)

For HTTP-based MCP servers (you must start the server first):

```bash
python scripts/evaluation.py \
  -t http \
  -u https://example.com/mcp \
  -H "Authorization: Bearer token123" \
  evaluation.xml
```

## Command-Line Options

```
usage: evaluation.py [-h] [-t {stdio,sse,http}] [-m MODEL] [-c COMMAND]
                     [-a ARGS [ARGS ...]] [-e ENV [ENV ...]] [-u URL]
                     [-H HEADERS [HEADERS ...]] [-o OUTPUT]
                     eval_file

positional arguments:
  eval_file             Path to evaluation XML file

optional arguments:
  -h, --help            Show help message
  -t, --transport       Transport type: stdio, sse, or http (default: stdio)
  -m, --model           Claude model to use (default: claude-3-7-sonnet-20250219)
  -o, --output          Output file for report (default: print to stdout)

stdio options:
  -c, --command         Command to run MCP server (e.g., python, node)
  -a, --args            Arguments for the command (e.g., server.py)
  -e, --env             Environment variables in KEY=VALUE format

sse/http options:
  -u, --url             MCP server URL
  -H, --header          HTTP headers in 'Key: Value' format
```

## Output

The evaluation script generates a detailed report including:

- **Summary Statistics**:

  - Accuracy (correct/total)
  - Average task duration
  - Average tool calls per task
  - Total tool calls

- **Per-Task Results**:
  - Prompt and expected response
  - Actual response from the agent
  - Whether the answer was correct (✅/❌)
  - Duration and tool call details
  - Agent's summary of its approach
  - Agent's feedback on the tools

### Save Report to File

```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a my_server.py \
  -o evaluation_report.md \
  evaluation.xml
```

## Complete Example Workflow

Here's a complete example of creating and running an evaluation:

1. **Create your evaluation file** (`my_evaluation.xml`):

```xml
<evaluation>
   <qa_pair>
      <question>Find the user who created the most issues in January 2024. What is their username?</question>
      <answer>alice_developer</answer>
   </qa_pair>
   <qa_pair>
      <question>Among all pull requests merged in Q1 2024, which repository had the highest number? Provide the repository name.</question>
      <answer>backend-api</answer>
   </qa_pair>
   <qa_pair>
      <question>Find the project that was completed in December 2023 and had the longest duration from start to finish. How many days did it take?</question>
      <answer>127</answer>
   </qa_pair>
</evaluation>
```

2. **Install dependencies**:

```bash
pip install -r scripts/requirements.txt
export ANTHROPIC_API_KEY=your_api_key
```

3. **Run evaluation**:

```bash
python scripts/evaluation.py \
  -t stdio \
  -c python \
  -a github_mcp_server.py \
  -e GITHUB_TOKEN=ghp_xxx \
  -o github_eval_report.md \
  my_evaluation.xml
```

4. **`github_eval_report.md`でレポートを確認**して:
   - どの質問が合格/不合格だったかを確認
   - ツールに関するエージェントのフィードバックを読む
   - 改善領域を特定
   - MCP サーバー設計を反復

## トラブルシューティング

### 接続エラー

接続エラーが発生した場合:

- **STDIO**: コマンドと引数が正しいことを確認
- **SSE/HTTP**: URL がアクセス可能で、ヘッダーが正しいことを確認
- 必要な API キーが環境変数またはヘッダーに設定されていることを確認

### 低い精度

多くの評価が失敗する場合:

- 各タスクのエージェントのフィードバックを確認
- ツールの説明が明確で包括的かチェック
- 入力パラメータが十分にドキュメント化されているか確認
- ツールが返すデータが多すぎるか少なすぎるか検討
- エラーメッセージが実行可能であることを確認

### タイムアウトの問題

タスクがタイムアウトする場合:

- より強力なモデルを使用（例: `claude-3-7-sonnet-20250219`）
- ツールが返すデータが多すぎないかチェック
- ページネーションが正しく機能しているか確認
- 複雑な質問を簡素化することを検討
