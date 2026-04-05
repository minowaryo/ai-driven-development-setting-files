# AI駆動開発 設定ファイルテンプレート

Laravel + PostgreSQL を前提とした AI駆動開発（Claude Code / Codex 併用）のリポジトリテンプレートです。

## 概要

このリポジトリは以下を含むテンプレートです:
- **Claude Code 用ルールファイル** (`CLAUDE.md`, `.claude/rules/`, `.claude/commands/`)
- **Codex 用指示ファイル** (`AGENTS.md`)
- **AI向け要約ドキュメント** (`docs/ai-context/`)
- **設計ドキュメントテンプレート** (`docs/product/`, `docs/architecture/`, `docs/adr/`)
- **開発プロセスドキュメント** (`docs/development/`, `docs/security/`)

## 3層構造

```
AIルール層        → CLAUDE.md, AGENTS.md, .claude/rules/
人間の設計知識層   → docs/architecture/, docs/product/, docs/adr/
AI向け要約層      → docs/ai-context/
```

## ファイル構成

```
.
├── CLAUDE.md                          # Claude Code エントリポイント
├── AGENTS.md                          # Codex エントリポイント
│
├── .claude/
│   ├── rules/
│   │   ├── 00-global.md               # 全体方針・開発フロー
│   │   ├── 10-laravel.md              # Laravel固有ルール
│   │   ├── 20-postgresql.md           # PostgreSQL固有ルール
│   │   ├── 30-testing.md              # テスト方針
│   │   ├── 40-security.md             # セキュリティルール
│   │   ├── 50-review.md               # レビュー観点
│   │   └── 60-docs.md                 # ドキュメント更新ルール
│   └── commands/
│       ├── review.md                  # /review コマンド
│       └── adr.md                     # /adr コマンド
│
└── docs/
    ├── ai-context/                    # AI向け要約層（最重要）
    │   ├── project-summary.md         # プロジェクト全体要約
    │   ├── module-map.md              # ディレクトリ担当一覧
    │   ├── common-commands.md         # よく使うコマンド集
    │   ├── glossary.md               # 用語集
    │   ├── do-not-touch.md           # 触ってはいけない領域
    │   └── prompt-patterns.md        # プロンプト定型文
    ├── product/                       # ビジネス要件
    │   ├── requirements.md
    │   ├── use-cases.md               # ★最重要：人間レビュー最終ポイント
    │   └── acceptance-criteria.md
    ├── architecture/                  # システム設計
    │   ├── overview.md
    │   ├── data-model.md
    │   └── authz-authn.md
    ├── adr/                           # 意思決定記録
    │   ├── ADR-0001-use-laravel.md
    │   ├── ADR-0002-use-postgresql.md
    │   ├── ADR-0003-auth-strategy.md
    │   └── ADR-0004-ai-development-policy.md
    ├── development/                   # 開発プロセス
    │   ├── coding-standards.md
    │   ├── testing-strategy.md
    │   ├── review-checklist.md
    │   └── ai-workflow.md
    ├── security/
    │   └── secrets-handling.md
    └── rcid/
        └── traceability-matrix.md
```

## 使い始め方

1. このリポジトリをテンプレートとして新しいプロジェクトにコピーする
2. `[PROJECT_NAME]` などのプレースホルダーをプロジェクト固有の情報に置き換える
3. `docs/ai-context/project-summary.md` を最初に埋める
4. `docs/product/requirements.md` → `docs/product/use-cases.md` の順で要件を定義する
5. `docs/product/use-cases.md` を人間がレビュー・承認してからAIコード生成を開始する

## AI駆動開発パイプライン

```
requirements.md（人間定義）
      ↓
use-cases.md（人間レビュー ← ここが品質ゲート）
      ↓
data-model.md / openapi.yaml
      ↓
AI code generation（Claude Code / Codex）
      ↓
AI test generation
      ↓
人間レビュー・マージ
```

## 参考資料

- [Claude Code ドキュメント](https://docs.anthropic.com/claude/docs/claude-code)
- [Codex AGENTS.md ガイド](https://platform.openai.com/docs/codex)
