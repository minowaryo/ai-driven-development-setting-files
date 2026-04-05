# CLAUDE.md

## Project

- **Stack**: Laravel + PostgreSQL
- **Type**: Monolith web application
- **Main domains**: [プロジェクト固有のドメインをここに記載]

## Read first (every session)

- `docs/ai-context/project-summary.md`
- `docs/ai-context/module-map.md`
- `docs/ai-context/common-commands.md`

## Read when relevant (task-based)

| Task type | Read this |
|---|---|
| Auth / authorization changes | `docs/architecture/authz-authn.md` |
| DB schema changes | `docs/architecture/data-model.md` + `docs/adr/` |
| Architecture / core design changes | `docs/adr/` |
| Adding / modifying tests | `docs/development/testing-strategy.md` |
| Security-related changes | `docs/security/secrets-handling.md` |
| Release / deployment | `docs/operations/deployment.md` |

## Global rules

- **Workflow**: Explore → Plan → Implement → Test の順で進める
- 先にドキュメントを確認してからコードに触る
- 大規模変更の前は必ず `docs/adr/` を確認する
- Authorization は Policy / Gate を必ず通す（バイパス禁止）
- DBスキーマ変更はマイグレーション計画なしに行わない
- 小さい差分を優先する（大きな1コミットより小さな複数コミット）
- 設計意図が変わるときはドキュメントも更新する

## Detailed rules

詳細ルールは `.claude/rules/` を参照:

- `.claude/rules/00-global.md` - 全体方針
- `.claude/rules/10-laravel.md` - Laravel固有ルール
- `.claude/rules/20-postgresql.md` - PostgreSQL固有ルール
- `.claude/rules/30-testing.md` - テスト方針
- `.claude/rules/40-security.md` - セキュリティ
- `.claude/rules/50-review.md` - レビュー観点
- `.claude/rules/60-docs.md` - ドキュメント更新ルール
