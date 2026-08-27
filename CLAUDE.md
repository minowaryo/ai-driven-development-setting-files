# CLAUDE.md

## Project

- **Project name**: [PROJECT_NAME]
- **Stack**: [例: Laravel + MySQL]
- **Type**: [例: Monolith web application / API / etc.]
- **Main domains**: [例: 在庫管理 / 受注処理 / etc.]
- **Repository**: [GitLab/GitHub URL]

---

## ⚠️ プロジェクト開始前の必須手順（Gate 0）

このリポジトリをクローンしたら、コードに触れる前に **`SETUP.md`** に記載の初回セットアップ手順を完了すること。
AIはそこに列挙されたファイルが埋まっていない状態では正確な支援ができない。`SETUP.md` はプロジェクト開始時に
一度だけ読むファイルであり、以下の毎セッション読み込みリストには含まれない。

---

## Read first (every session)

- `docs/ai-context/project-summary.md`
- `docs/ai-context/glossary.md`
- `docs/ai-context/module-map.md`

## Read when relevant (task-based)

| Task type | Read this |
|---|---|
| requirements.md / use-cases.md 作成・更新 | `docs/original-docs/`（一次資料参照） + `docs/product/requirements.md` |
| テスト実行・マイグレーション・ビルド等のコマンド操作 | `docs/ai-context/common-commands.md` |
| 要件確認・UC参照 | `docs/product/requirements.md` + `docs/product/use-cases.md` |
| コード実装（機能開発） | `docs/product/use-cases.md` + `docs/architecture/data-model.md` + `docs/product/mockups/` |
| UI実装・モックベースの開発 | `docs/product/ui-guidelines.md` + `docs/product/mockups/` |
| Frontend component changes | `.claude/rules/15-frontend.md`（選定したフロントエンドスタックの実装ルール。`meta/adr/ADR-0005-frontend-stack.md` 参照） |
| Auth / authorization changes | `docs/architecture/authz-authn.md` + `docs/product/org-permission-philosophy.md`（権限・ロールのビジネス方針） |
| DB schema changes | `docs/architecture/data-model.md` + `docs/adr/` |
| Architecture / core design changes | `docs/adr/` |
| Adding / modifying tests | `docs/development/testing-strategy.md` + `docs/product/use-cases.md` + `docs/architecture/data-model.md` |
| Security-related changes | `docs/security/secrets-handling.md` |
| 認証情報・APIキー等の作成 | `docs/credentials/`（`.claude/rules/40-security.md` の取り扱いルールに従う） |
| Release / deployment | `docs/operations/deployment.md` |
| Change request (CR) 発生時 | `docs/rcid/traceability-matrix.md` |
| ユーザー向け機能・操作方法の変更 | `docs/product/user-guide.md` |
| UAT（受け入れテスト）実施時（任意） | `docs/product/uat-scenarios.md` + `docs/product/uat-results/`（`.claude/rules/00-global.md` のUAT節を参照。非ブロッキング） |
| エラー・ライブラリ固有の詰まりに遭遇した時 | `docs/ai-context/known-pitfalls.md`（既知の事象がないか先に確認し、解決したら追記） |

## Global rules

- **Workflow**: Explore → Plan → Implement → Test の順で進める
- セッション開始時は必ず `docs/ai-context/` を読む
- **Gate 2（use-cases.md 承認）が完了するまでコード生成を行わない**
- **Gate 4（テストケース承認）が完了するまで実装（Greenフェーズ）に着手しない**（`.claude/rules/30-testing.md`）
- `docs/original-docs/` は参照のみ（編集・削除・ファイル作成禁止）
- 先にドキュメントを確認してからコードに触る
- 大規模変更の前は必ず `docs/adr/` を確認する
- Authorization は Policy / Gate を必ず通す（バイパス禁止）
- DBスキーマ変更はマイグレーション計画なしに行わない
- 小さい差分を優先する（大きな1コミットより小さな複数コミット）
- 設計意図が変わるときはドキュメントも更新する

## Detailed rules

詳細ルールは `.claude/rules/` を参照:

- `.claude/rules/00-global.md` - 全体方針・開発フロー・品質ゲート
- `.claude/rules/10-laravel.md` - Laravel固有ルール
- `.claude/rules/15-frontend.md` - フロントエンド固有ルール（内容は `meta/adr/ADR-0005-frontend-stack.md` の選定結果に応じてプロジェクトごとに書き換わる。デフォルト内容は Vue.js + Inertia.js）
- `.claude/rules/20-mysql.md` - MySQL固有ルール
- `.claude/rules/30-testing.md` - テスト方針（Feature/Unit・TDD）
- `.claude/rules/31-e2e-testing.md` - E2Eテスト方針（Playwright。`/generate-e2e-test` 実行時のみ参照）
- `.claude/rules/40-security.md` - セキュリティ
- `.claude/rules/50-review.md` - レビュー観点
- `.claude/rules/60-docs.md` - ドキュメント更新ルール
