# ADR-0002: PostgreSQL をデータベースとして採用

## Status
Accepted

## Date
[YYYY-MM-DD]

## Context

バックエンドのデータベースを選定する必要がある。
アプリケーションはトランザクション整合性が求められる業務データを扱う。
将来的な拡張性とJSON系データの扱いも考慮する。

## Decision

PostgreSQL を採用する。

## Rationale

**採用理由:**
- ACID準拠の強力なトランザクション管理
- JSON / JSONB型のサポート（柔軟なデータ構造に対応）
- 全文検索機能が標準搭載（pg_trgm等）
- 行レベルセキュリティ（RLS）のサポート
- Laravel / Eloquent との統合が優秀
- オープンソースで無償利用可能

### 採用しなかった代替案
- **MySQL**: JSONサポートや高度なクエリ機能でPostgreSQLに劣る
- **SQLite**: スケールアウトに不向き（本番環境）
- **MongoDB**: ACID保証が弱く、業務データには不向き

## Consequences

### メリット
- 複雑なクエリ・集計が得意
- データ整合性が強い
- 将来的なJSONB活用で柔軟性が高い

### デメリット・リスク
- MySQLと比べてホスティングコストがやや高い場合がある
- チューニングにPostgreSQL固有の知識が必要

## Related
- ADR-0001: Laravel採用
- `.claude/rules/20-postgresql.md`
- `docs/architecture/data-model.md`
