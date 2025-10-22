# スキル

スキルは、Claude が専門的なタスクのパフォーマンスを向上させるために動的に読み込む、指示、スクリプト、リソースを含むフォルダです。スキルは、会社のブランドガイドラインに沿った文書の作成、組織固有のワークフローを使用したデータ分析、個人的なタスクの自動化など、特定のタスクを再現可能な方法で完了する方法を Claude に教えます。

詳細については、以下をご覧ください：

- [スキルとは？](https://support.claude.com/en/articles/12512176-what-are-skills)
- [Claude でスキルを使用する](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [カスタムスキルの作成方法](https://support.claude.com/en/articles/12512198-creating-custom-skills)
- [エージェントスキルで実世界に対応したエージェントを構築](https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

# このリポジトリについて

このリポジトリには、Claude のスキルシステムで可能なことを示すサンプルスキルが含まれています。これらの例は、クリエイティブなアプリケーション（アート、音楽、デザイン）から技術的なタスク（Web アプリのテスト、MCP サーバー生成）、エンタープライズワークフロー（コミュニケーション、ブランディングなど）まで多岐にわたります。

各スキルは、Claude が使用する指示とメタデータを含む`SKILL.md`ファイルを持つ独立したディレクトリに格納されています。これらの例を参照して、独自のスキルのインスピレーションを得たり、さまざまなパターンやアプローチを理解したりできます。

このリポジトリのサンプルスキルはオープンソース（Apache 2.0）です。また、[Claude の文書機能](https://www.anthropic.com/news/create-files)の基盤となっている文書作成・編集スキルを[`document-skills/`](./document-skills/)フォルダに含めています（訳註：次の文にある通り、オープンソースとしてのライセンスが確認できないことから、このフォルダはリポジトリから削除しました）。これらはソース公開であり、オープンソースではありませんが、本番環境の AI アプリケーションで実際に使用されているより複雑なスキルの参考として開発者と共有したいと考えました。

**注意：** これらは学習とインスピレーションのための参考例です。組織固有のワークフローや機密コンテンツではなく、汎用的な機能を示しています。

## 免責事項

**これらのスキルはデモンストレーションと教育目的のみで提供されています。** これらの機能の一部は Claude で利用できる場合がありますが、Claude から受け取る実装と動作は、これらの例で示されているものとは異なる場合があります。これらの例は、パターンと可能性を示すことを目的としています。重要なタスクに依存する前に、必ず自分の環境でスキルを十分にテストしてください。

# サンプルスキル

このリポジトリには、さまざまな機能を示す多様なサンプルスキルのコレクションが含まれています：

## クリエイティブ & デザイン

- **algorithmic-art** - シード付きランダム性、フローフィールド、パーティクルシステムを使用して p5.js でジェネラティブアートを作成
- **canvas-design** - デザイン哲学を使用して、.png および.pdf 形式の美しいビジュアルアートをデザイン
- **slack-gif-creator** - Slack のサイズ制約に最適化されたアニメーション GIF を作成

## 開発 & 技術

- **artifacts-builder** - React、Tailwind CSS、shadcn/ui コンポーネントを使用して複雑な claude.ai HTML アーティファクトを構築
- **mcp-server** - 外部 API やサービスを統合する高品質な MCP サーバーを作成するためのガイド
- **webapp-testing** - Playwright を使用してローカル Web アプリケーションをテストし、UI の検証とデバッグを実行

## エンタープライズ & コミュニケーション

- **brand-guidelines** - Anthropic の公式ブランドカラーとタイポグラフィをアーティファクトに適用
- **internal-comms** - ステータスレポート、ニュースレター、FAQ などの社内コミュニケーションを作成
- **theme-factory** - 10 のプリセットされたプロフェッショナルテーマでアーティファクトをスタイリングするか、カスタムテーマをその場で生成

## メタスキル

- **skill-creator** - Claude の機能を拡張する効果的なスキルを作成するためのガイド
- **template-skill** - 新しいスキルの出発点として使用する基本的なテンプレート

# ドキュメントスキル（訳註：日本語訳に際してリポジトリから削除）

`document-skills/`サブディレクトリには、Claude がさまざまなドキュメントファイル形式を作成できるようにするために Anthropic が開発したスキルが含まれています。これらのスキルは、複雑なファイル形式やバイナリデータを扱うための高度なパターンを示しています：

- **docx** - 変更履歴、コメント、書式保持、テキスト抽出をサポートした Word 文書の作成、編集、分析
- **pdf** - テキストとテーブルの抽出、新規 PDF 作成、文書の結合/分割、フォーム処理を行う包括的な PDF 操作ツールキット
- **pptx** - レイアウト、テンプレート、グラフ、自動スライド生成をサポートした PowerPoint プレゼンテーションの作成、編集、分析
- **xlsx** - 数式、書式設定、データ分析、可視化をサポートした Excel スプレッドシートの作成、編集、分析

**重要な免責事項：** これらのドキュメントスキルはある時点のスナップショットであり、積極的に保守または更新されていません。これらのスキルのバージョンは Claude に事前に含まれています。これらは主に、バイナリファイル形式や文書構造を扱うより複雑なスキルの開発に Anthropic がどのようにアプローチしているかを示す参考例として意図されています。

# Claude Code、Claude.ai、API で試す

## Claude Code

このリポジトリを Claude Code プラグインマーケットプレイスとして Claude Code で以下のコマンドを実行して登録できます：

```
/plugin marketplace add anthropics/skills
```

次に、特定のスキルセットをインストールするには：

1. `Browse and install plugins`を選択
2. `anthropic-agent-skills`を選択
3. `document-skills`または`example-skills`を選択
4. `Install now`を選択

または、以下のコマンドでプラグインを直接インストールできます：

```
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
```

プラグインをインストールした後、言及するだけでスキルを使用できます。たとえば、マーケットプレイスから`document-skills`プラグインをインストールした場合、Claude Code に次のように依頼できます：「PDF スキルを使用して、path/to/some-file.pdf からフォームフィールドを抽出してください」

## Claude.ai

これらのサンプルスキルはすべて、Claude.ai の有料プランですでに利用可能です。

このリポジトリの任意のスキルを使用したり、カスタムスキルをアップロードしたりするには、[Claude でスキルを使用する](https://support.claude.com/en/articles/12512180-using-skills-in-claude#h_a4222fa77b)の手順に従ってください。

## Claude API

Claude API を介して、Anthropic の事前構築されたスキルを使用したり、カスタムスキルをアップロードしたりできます。詳細については、[スキル API クイックスタート](https://docs.claude.com/en/api/skills-guide#creating-a-skill)を参照してください。

# 基本的なスキルの作成

スキルの作成は簡単です - YAML フロントマターと指示を含む`SKILL.md`ファイルがあるフォルダだけです。このリポジトリの**template-skill**を出発点として使用できます：

```markdown
---
name: my-skill-name
description: このスキルが何をするのか、いつ使用するのかを明確に説明
---

# My Skill Name

[このスキルがアクティブなときに Claude が従う指示をここに追加]

## 例

- 使用例 1
- 使用例 2

## ガイドライン

- ガイドライン 1
- ガイドライン 2
```

フロントマターには 2 つのフィールドのみが必要です：

- `name` - スキルの一意の識別子（小文字、スペースにはハイフン）
- `description` - スキルが何をするのか、いつ使用するのかの完全な説明

以下のマークダウンコンテンツには、Claude が従う指示、例、ガイドラインが含まれます。詳細については、[カスタムスキルの作成方法](https://support.claude.com/en/articles/12512198-creating-custom-skills)を参照してください。

# パートナースキル

スキルは、特定のソフトウェアの使用方法を Claude に教える優れた方法です。パートナーから素晴らしいサンプルスキルが提供された場合、ここでそれらのいくつかを紹介する可能性があります：

- **Notion** - [Notion Skills for Claude](https://www.notion.so/notiondevs/Notion-Skills-for-Claude-28da4445d27180c7af1df7d8615723d0)
