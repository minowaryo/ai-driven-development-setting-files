# 60-docs.md — ドキュメント更新ルール

## 変更とドキュメントの対応表

| 変更内容 | 更新すべきドキュメント |
|---|---|
| DB スキーマ変更 | `docs/architecture/data-model.md` |
| API 追加・変更 | `docs/architecture/overview.md` |
| 認証認可変更 | `docs/architecture/authz-authn.md` |
| アーキテクチャ判断 | `docs/adr/ADR-XXXX-xxx.md`（新規作成） |
| コーディング規約変更 | `docs/development/coding-standards.md` |
| テスト方針変更 | `docs/development/testing-strategy.md` |
| 新しい共通コマンド | `docs/ai-context/common-commands.md` |
| 新ドメイン・モジュール追加 | `docs/ai-context/module-map.md` |
| 用語追加 | `docs/ai-context/glossary.md` |
| 触ってはいけない領域の変更 | `docs/ai-context/do-not-touch.md` |
| UI/デザイン仕様変更 | `docs/product/ui-guidelines.md` |
| モック追加・更新 | `docs/product/mockups/README.md`（画面一覧を更新） |
| UCへのモックフィードバック反映 | `docs/product/use-cases.md` + `docs/product/mockups/README.md` |
| フロントエンド画面・コンポーネント追加 | `docs/ai-context/module-map.md` |
| 状態管理（Pinia/Vuex/Redux等、選定したスタックのStore）の追加・変更 | `docs/architecture/overview.md` |
| フロントエンド技術選定（ライブラリ変更等） | `docs/adr/ADR-XXXX-xxx.md`（新規作成） |
| 権限・ロールのビジネス方針変更 | `docs/product/org-permission-philosophy.md` + `docs/architecture/authz-authn.md` |
| ユーザー向け機能・操作方法の変更 | `docs/product/user-guide.md` |
| UATシナリオ・結果の追加（任意） | `docs/product/uat-scenarios.md` / `docs/product/uat-results/`（`.claude/rules/00-global.md` のUAT節を参照。非ブロッキング） |
| ライブラリ/フレームワーク固有のハマりどころを解決した | `docs/ai-context/known-pitfalls.md`（常時読込ではないため、コード変更と同一コミットである必要はない。解決した都度追記） |
| 新しいデータモデル追加（CRUD網羅） | `.claude/rules/30-testing.md`（CRUD網羅ルール）参照 |
| 開発/テスト用credentialやAPIキーの保管場所を新たに記録した | `docs/credentials/README.md`（実際のsecret値はコミットしない） |
| エラーハンドリング・レスポンス形式の規約変更 | `docs/development/coding-standards.md` |
| Gate条件・品質ゲート運用の変更 | `.claude/rules/00-global.md`（詳細表・絶対禁止）+ `SETUP.md`（Step手順）+ `AGENTS.md`（Codex用。Gate定義を複製しているため3ファイル同期が必要） |
| 人間/AIの役割分担の変更（新しい導入パス・新しいAI機能等） | `docs/development/ai-workflow.md`（役割分担）+ ポリシーレベルの変更であれば `meta/adr/ADR-0004` への一行の改訂注記（2026-07-15 / 2026-09-15の改訂注記のスタイルを参照） |
| 新しいAIエントリポイントの追加（スキルまたはコマンド） | `docs/ai-context/common-commands.md`（エントリポイント表）+ `README.md`（ディレクトリツリー）。`.claude/skills/` か `.claude/commands/` かは `meta/adr/ADR-0012-skills-vs-commands.md` の基準で判断する |
| Gitワークフローの変更（ブランチ・コミット・push・マージ・マージ前チェック） | `docs/development/git-workflow.md`（常時適用のコアのみ `.claude/rules/70-git.md` も）——他のファイルは1行のポインタまで（ポリシーレベルの変更なら ADR も。`meta/adr/ADR-0015` 参照） |

## ドキュメント更新の原則

1. **コード変更と同じコミットでドキュメントも更新する**
2. 仕様変更はドキュメント先行（コード前に文書化）
3. ADRは「なぜそう決めたか」を必ず書く（Whatだけでなく Why）
4. `docs/ai-context/` は短く・正確に保つ（AIが読む要約層）

## ADRを書くべきタイミング

以下の判断をしたときは必ずADRを作成する:

- 新しいライブラリ・フレームワークの採用
- 既存ライブラリの変更・廃止
- アーキテクチャパターンの変更
- セキュリティ方針の変更
- DBスキーマの大規模変更
- AI開発ポリシーの変更

## PLAN.md 肥大化防止ルール（アーカイブ運用）

`PLAN.md`はセッションをまたいで参照する現在進行中のタスク台帳であり、無制限に追記し続けると1ファイルが肥大化し逆に参照性が落ちる。以下のルールで一定サイズ以内に保つ。

- **上限**: `PLAN.md`は**300行を超えないようにする**（250行を超えた時点でアーカイブ実施を検討する目安とする）
- **アーカイブ先**: `docs/history/plan-archive.md`(プロジェクト内に存在しない場合は新規作成する)。ハーネステンプレートのリポジトリ自身（`APPLY_TEMPLATE.md` を含むリポジトリ）でのみ、代わりに `meta/history/plan-archive.md` を使う（テンプレート内部用で、導入先には決して届かない — `APPLY_TEMPLATE.md` の class X、`SETUP.md` で削除）
- **手順**（何をどう移すか・「アーカイブ済み」注記）: `docs/development/plan-archiving.md`

## ADRテンプレート

`.claude/commands/adr.md`（`/adr` コマンド）のテンプレートを使う——「見送り（不採用）を記録する場合のバリエーション」を含め、同ファイルが唯一の正本である。
