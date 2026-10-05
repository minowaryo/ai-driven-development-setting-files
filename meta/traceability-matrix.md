# traceability-matrix.md — ハーネスのスクリプト

> テンプレート内部用（`APPLY_TEMPLATE.md` の class X。`SETUP.md` で削除する）。`.claude/hooks/` の
> すべてのスクリプトについて、どこで実行されるか、コストはどれくらいか、なぜ存在するか、どうテストするかを
> 追跡する。プロジェクト自身の要件 ↔ ユースケース ↔ コード ↔ テストのマトリクスは
> `docs/rcid/traceability-matrix.md` である（`/regenerate-traceability` が作り直すが、このファイルには
> 決して触れない）。
> スクリプトを追加・変更するコミットで、同時に該当の行を更新する。

## スクリプト

| スクリプト | 役割 | 実行元 | 頻度 | 1回あたりのコスト | 決定 | テスト |
|---|---|---|---|---|---|---|
| `review-score.sh` | ブランチの差分をスコアリングする: レビュー強度 + マージ前チェックの区分 | `/review` Step 0、`prepare-merge` ステップ2 | `/review` のたび、マージのたび | 約1.1秒 | ADR-0009、ADR-0015 | `meta/tests/review-score.test.sh` |
| `domain-boundary-check.sh` | Domain Boundary を越えるControllerを検出する | `/review` Step 0、`prepare-merge` ステップ2、`/onboard-existing-codebase` Step 1B（`--audit-all`） | `/review` のたび、マージのたび、オンボーディング時に1回 | ブランチあたり約0.4秒、`--audit-all` で54,000行あたり約2.9秒 | ADR-0010（+ 2026-10-03 の更新メモ）、ADR-0015 の更新メモ | `meta/tests/domain-boundary-check.test.sh` |
| `spec-lint.sh` | 要件定義・ユースケース・モックの構造チェック（EN版・JP版のテンプレート両方） | AIが Gate 1 / Gate 2 / Gate 0〜3 の一括承認の前に実行（`CLAUDE.md`、`AGENTS.md`、`SETUP.md` Step 2 / 2B、`/onboard-existing-codebase` Step 2B） | プロジェクトあたり数回 | 約1秒 | ADR-0018 | `meta/tests/spec-lint.test.sh` |

Loop Engineering 段階1のスクリプト（`agent-guard.sh`、`tdd-snapshot.sh`）はEN版と社内版にはあるが、
JP版には移植していない（ADR-0016 の段階4で移植予定）。

コスト: Windows の Git Bash で 2026-10-05 に計測。どのスクリプトもAIモデルを呼び出さないので、出力する
行の分を除いてトークンは消費しない。

## 同期

兄弟リポジトリ間でバイト単位で一致（改行コードの違いは無視）、2026-10-06 に確認。
EN = `ai-driven-development-setting-files-en`、社内版 = `ai-driven-development-setting-files-en-company`。

| スクリプト | 追加（JP） | 最終変更（JP） | EN | 社内版 |
|---|---|---|---|---|
| `review-score.sh` | `3c2332c` | `140f3c1` | 一致 | 一致 |
| `domain-boundary-check.sh` | `37017ca` | `12085ed` | 一致 | 一致 |
| `spec-lint.sh` | `b1c13a6` | `b1c13a6` | 一致 | 一致 |
