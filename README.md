# AI駆動開発 設定ファイルテンプレート

Laravel + MySQL を前提とした AI駆動開発（Claude Code / Codex 併用）のリポジトリテンプレートです。

## 概要

このリポジトリは以下を含むテンプレートです:
- **Claude Code 用ルールファイル** (`CLAUDE.md`, `.claude/rules/`, `.claude/commands/`, `.claude/hooks/`)
- **Codex 用指示ファイル** (`AGENTS.md`)
- **ユーザーグローバル設定テンプレート** (`GLOBAL_CLAUDE.md`) — `~/.claude/CLAUDE.md` にコピーして使う。commit/push の実行可否など環境全体のルールを持つ
- **プロジェクト初回セットアップガイド** (`SETUP.md`) — Gate 0〜4 のセットアップ手順。新規プロジェクト開始時に一度だけ読む（既にコードが存在するプロジェクトへの導入にも対応——`SETUP.md` の既存コードベース導入パスを参照）
- **AI向け要約ドキュメント** (`docs/ai-context/`)
- **設計ドキュメントテンプレート** (`docs/product/`, `docs/architecture/`, `docs/adr/`)
- **開発プロセスドキュメント** (`docs/development/`, `docs/security/`)
- **テンプレート自身のメタADR** (`meta/adr/`) — このテンプレート/AI開発ハーネス自体の設計判断の記録。プロジェクト側の意思決定（`docs/adr/`）とは別管理

## 5層構造

```
AIルール層          → CLAUDE.md, AGENTS.md, .claude/rules/
人間の設計知識層     → docs/architecture/, docs/product/, docs/adr/
AI向け要約層        → docs/ai-context/
一次資料層          → docs/original-docs/（人間入力・AI編集禁止・Step1〜Step2のみ参照）
テンプレート自身の層 → meta/adr/（プロジェクトの意思決定サイクルには属さない。編集・リナンバリング不要）
```

## ファイル構成

```
.
├── CLAUDE.md                          # Claude Code エントリポイント
├── AGENTS.md                          # Codex エントリポイント
├── SETUP.md                           # プロジェクト初回セットアップガイド（Gate 0〜4 のセットアップ手順）
├── GLOBAL_CLAUDE.md                   # ~/.claude/CLAUDE.md 用テンプレート（ユーザーグローバル設定。各自の環境にコピーして使う）
│
├── PLAN.md                            # 開発計画（進行中タスク管理）
├── .mcp.json                          # プロジェクトスコープMCP（Playwright等）
├── .claude/
│   ├── rules/
│   │   ├── 00-global.md               # 全体方針・開発フロー・品質ゲート
│   │   ├── 10-laravel.md              # Laravel固有ルール
│   │   ├── 15-frontend.md             # フロントエンド固有ルール（ADR-0005の選定結果で内容が決まる。デフォルトはVue.js+Inertia.js）
│   │   ├── 20-mysql.md                # MySQL固有ルール
│   │   ├── 30-testing.md              # テスト方針（Feature/Unit・TDD）
│   │   ├── 31-e2e-testing.md          # E2Eテスト方針（Playwright。/generate-e2e-test 実行時のみ読む）
│   │   ├── 40-security.md             # セキュリティルール
│   │   ├── 50-review.md               # レビュー観点
│   │   └── 60-docs.md                 # ドキュメント更新ルール
│   ├── agents/
│   │   ├── test-writer.md             # TDD Redフェーズ専用サブエージェント
│   │   └── tdd-implementer.md         # TDD Greenフェーズ専用サブエージェント
│   ├── commands/
│   │   ├── review.md                  # /review コマンド
│   │   ├── adr.md                     # /adr コマンド
│   │   ├── generate-mock.md           # /generate-mock コマンド
│   │   ├── tdd.md                     # /tdd コマンド（Red→Green→Refactor）
│   │   ├── generate-e2e-test.md       # /generate-e2e-test コマンド
│   │   └── onboard-existing-codebase.md  # /onboard-existing-codebase コマンド（既存コードベース導入パス Step 1B〜3B）
│   └── hooks/
│       ├── domain-boundary-check.sh   # ドメイン境界契約チェック（/review Step 0で実行。--audit-all でリポジトリ全体監査）
│       └── review-score.sh            # ブランチ差分をスコアリングしレビュー強度を決定（/review Step 0）
│
├── meta/
│   └── adr/                           # テンプレート/ハーネス自身のADR（プロジェクトのADRとは別管理。編集・リナンバリング不要）
│       ├── README.md
│       ├── ADR-0001-use-laravel.md
│       ├── ADR-0002-use-mysql.md
│       ├── ADR-0003-auth-strategy.md
│       ├── ADR-0004-ai-development-policy.md
│       ├── ADR-0005-frontend-stack.md
│       ├── ADR-0006-e2e-testing-playwright.md
│       ├── ADR-0007-tdd-enforcement-probity.md
│       ├── ADR-0008-tdd-e2e-harness-tooling.md
│       ├── ADR-0009-review-escalation-mechanism.md
│       ├── ADR-0011-domain-boundary-contract.md      # ※0010はスキル化基準ADR（保留中の下書き）のため欠番
│       └── ADR-0012-existing-codebase-adoption.md
│
└── docs/
    ├── ai-context/                    # AI向け要約層（最重要）
    │   ├── project-summary.md         # プロジェクト全体要約
    │   ├── module-map.md              # ディレクトリ担当一覧
    │   ├── common-commands.md         # よく使うコマンド集
    │   ├── glossary.md                # 用語集
    │   ├── do-not-touch.md            # 触ってはいけない領域
    │   └── known-pitfalls.md          # ライブラリ/FW固有の落とし穴（エラー時に参照し、解決したら追記）
    ├── original-docs/                 # 人間が持ち込む一次資料（AI編集禁止・参照のみ可）
    │   └── README.md                  # ファイル一覧・用途メモ
    ├── product/                       # ビジネス要件・UIデザイン
    │   ├── requirements.md
    │   ├── use-cases.md               # ★最重要：人間レビュー最終ポイント
    │   ├── acceptance-criteria.md
    │   ├── ui-guidelines.md           # UIデザイン仕様・コンポーネント方針
    │   ├── org-permission-philosophy.md  # 権限・ロールの業務側の考え方
    │   ├── user-guide.md              # エンドユーザー向けの機能・操作ドキュメント
    │   ├── uat-scenarios.md           # UATシナリオ（任意・非ブロッキング。Gate 0〜4の外）
    │   ├── uat-results/               # UAT結果（人間のレビュアーが記録）
    │   │   └── README.md
    │   └── mockups/                   # HTMLモック（Gate1通過後〜Gate2の間に作成）
    │       └── README.md              # 画面一覧・UC対応表
    ├── architecture/                  # システム設計
    │   ├── overview.md
    │   ├── data-model.md
    │   └── authz-authn.md
    ├── adr/                           # 意思決定記録（プロジェクト自身のADR。テンプレート適用直後は空 = ADR-0001から採番開始）
    │   └── README.md                  # 役割メモ（meta/adr/ との違い）
    ├── development/                   # 開発プロセス
    │   ├── coding-standards.md
    │   ├── testing-strategy.md
    │   ├── review-checklist.md
    │   └── ai-workflow.md
    ├── security/
    │   └── secrets-handling.md
    ├── credentials/                   # 開発用の認証情報の所在メモ（README以外はgit管理外。実際の秘密情報は置かない）
    │   └── README.md
    └── rcid/
        └── traceability-matrix.md
```

## 使い始め方

> **既にコードが存在するプロジェクトへ導入する場合**: 以下（および `SETUP.md` のStep 1〜4）は新規
> プロジェクト（既存コードなし）を前提としている。代わりに `SETUP.md` の既存コードベース導入パスを
> 参照すること——同じGate 0〜3の入力を、人間が白紙から作るのではなく実際のコードベースから逆生成する。

詳細なGate 0〜4の手順は `SETUP.md` を参照。以下は概要:

1. このリポジトリをテンプレートとして新しいプロジェクトにコピーする
2. `[PROJECT_NAME]` などのプレースホルダーをプロジェクト固有の情報に置き換える
3. 手元の一次資料（要件メモ・画面スケッチ等）を `docs/original-docs/` に置く
4. `docs/original-docs/` を参照しながら `docs/ai-context/` の必須ファイルを埋める（Gate 0）
5. `docs/original-docs/` を参照しながら `docs/product/requirements.md` → `docs/product/use-cases.md` の順で要件を定義する（Gate 1〜2の間）
6. `/generate-mock` でHTMLモックを生成し、ビジネス側にレビューしてもらう
7. フィードバックを `use-cases.md` に反映し、人間が最終承認する（Gate 2）
8. `docs/architecture/data-model.md` を設計・承認してからAIコード生成を開始する（Gate 3）
9. `/tdd` コマンドでRed（失敗するテスト作成）→ テストケース承認（Gate 4）→ Green（実装）→ Refactor の順で進める

## AI駆動開発パイプライン

```
[Gate 0] docs/ai-context/ 記入完了
      ↓
[Gate 1] requirements.md — レビュアー承認
      ↓  AIによる use-cases.md 叩き台生成可
use-cases.md 作成・修正
      ↓  AIによるモック生成可（/generate-mock）
モック作成 → ビジネス側レビュー → use-cases.md にフィードバック反映
      ↓
[Gate 2] use-cases.md — 最終承認 ★コード生成解禁
      ↓  AIによる data-model.md 叩き台生成可
[Gate 3] data-model.md — レビュアー承認
      ↓
AI テストケース生成（Red・/tdd コマンド）
      ↓
[Gate 4] テストケース承認 ★実装（Green）着手前にレビュアーが確認
      ↓
AI 実装コード生成（Green・Claude Code / Codex）
      ↓
Refactor
      ↓
人間レビュー・マージ
```

## 参考資料

- [Claude Code ドキュメント](https://docs.anthropic.com/claude/docs/claude-code)
- [Codex AGENTS.md ガイド](https://platform.openai.com/docs/codex)
