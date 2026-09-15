# PLAN.md

## 既存コードベース導入パスの追加（ENリポジトリからの移植） (2026-09-15)

### Decision

- 本ハーネスのGate 0〜4パイプラインはグリーンフィールド（新規開発。コードが存在する前に人間が白紙から `requirements.md` を書く）を前提としていた。既にコードが存在するプロジェクトへ本ハーネスを導入するための汎用的な「既存コードベース導入パス」を追加した: `SETUP.md` にStep 0の分岐を新設し（新規プロジェクト→従来のStep 1〜4のまま／既存コード→Step 1B〜3B、その後Step 4に合流）、`meta/adr/ADR-0012-existing-codebase-adoption.md` に記録した。単一リポジトリ・単一ファイル内でGate 0の内部に分岐を設ける構成であり、`ADR-0005`のフロントエンドスタック選定と同じパターンを踏襲した（フォークではない。Gate 1〜4と `.claude/rules/10〜60` はどちらのパスでも同一のため）。ENリポジトリの `ADR-0011` に相当する内容だが、本リポジトリでは0010番・0011番が既に別件（保留中のスキル化基準ADR／Domain Boundary契約）で使用済みのため、`ADR-0012`として採番した。
- Step 1Bは、`ADR-0005`がフロントエンドに対してのみ行っている「検出する、決めつけない」という扱いを、バックエンド・DB・認証方式にも一般化した（`ADR-0001`/`ADR-0002`/`ADR-0003`との突き合わせ）。PHP/Laravel系ですらない根本的な不一致の場合は、無理に適用せずその場で導入対象外と判定する。
- 出力は2本のリストに分離した: ブロッキングの**「要確認」**リスト（逆生成したドキュメント自体の信頼性に関わるものだけ。これを解消することが単一の統合Gate 0〜3承認になる）と、非ブロッキングの**「Backlog」**リスト（`domain-boundary-check.sh --audit-all` の指摘。`ADR-0011`自身の「積み残しであってブロッカーではない」という考え方をそのまま踏襲）。初期ドラフトではBacklogの指摘をブロッキング側に混ぜており、また組み込みスキル`security-review`をオンボーディングに組み込もうとしたが、いずれもレビューで修正・不採用とした——前者は`ADR-0011`の立場と矛盾し、後者はdiffスコープのため引き継いだアプリケーションコードを見られないことが判明したため。
- `/onboard-existing-codebase` コマンドを新設し、Step 1B〜3Bを一気通貫で自動化する。Claude Code組み込みの `/init` の拡張版・専用版という位置づけとし、`/init` をこのテンプレートに実行することは非推奨とした（本テンプレート固有の`CLAUDE.md`を汎用形式で上書きしてしまうため）。
- 分岐開始の検知は `.claude/rules/00-global.md` ではなく `docs/ai-context/project-summary.md` のプレースホルダー本文に持たせた——`00-global.md` は毎セッション読み込みリストに含まれておらず、後者のみが確実にセッション最初に読まれるため。
- `docs/development/ai-workflow.md` の役割分担を新規/既存コードベースの2表構成にし、`meta/adr/ADR-0004` に既存の2026-07-15形式を踏襲した改訂注記を追加した。`docs/original-docs/README.md` にも、既存の「一次情報源」記述（グリーンフィールド前提）と新しい「コードが正」の原則が矛盾して見えないよう、両者とも既存の `.claude/rules/00-global.md`「ユーザー向け挙動変更には常に承認が必要」ルールに基づくことを明記した。
- あえてルールブックにはしない設計とした——コードとドキュメントの食い違いのパターンを事前に網羅するのではなく、「迷ったら要確認リストに載せて必ず尋ねる」という一点のみを`SETUP.md`・新設コマンド・ADRの3箇所で明示した。

### Files touched

`meta/adr/ADR-0012-existing-codebase-adoption.md`（新規）、`SETUP.md`、`.claude/commands/onboard-existing-codebase.md`（新規）、`docs/development/ai-workflow.md`、`meta/adr/ADR-0004-ai-development-policy.md`、`docs/ai-context/project-summary.md`、`.claude/rules/00-global.md`、`AGENTS.md`、`README.md`、`docs/original-docs/README.md`、`.claude/rules/60-docs.md`、`meta/adr/README.md`。

### Status

完了。ドキュメントのみの変更（アプリケーションコードの変更なし、ビルド・テスト不要）。次のフォローアップなし。実際のドラフト挙動は、既存コードを持つプロジェクトで初めて本パスを使った際に検証される。

## Domain Boundaryの契約化と決定的なController検知（ENリポジトリからの移植） (2026-09-14)

### Decision

- ENリポジトリ（`ai-driven-development-setting-files-en`）で実施済みのDomain Boundary対応を本リポジトリに移植した。`.claude/rules/10-laravel.md` に **Domain Boundary**（Service/Actionレイヤー + Policyレイヤー）を、「Fat Controller禁止」という抽象的なラベルではなく明示的なMAY / MUST NOTリストとして明文化した。Controllerが行ってよいのはFormRequestによるバリデーション、`authorize()` の呼び出し、Service/Actionの呼び出し（ちょうど1つ）、レスポンスの整形のみであり、`DB::` の呼び出し、Eloquentの書き込みメソッドの呼び出し、ロールのインラインチェック、複数エンティティにまたがる判断は行ってはならない。
- `.claude/hooks/domain-boundary-check.sh`（決定的なgit + awkチェック、AI呼び出しなし）を新規追加し、`/review` のStep 0で `review-score.sh` と並べて実行するようにした。`db-access` / `eloquent-write` / `role-check` の指摘と、メソッド単位の分岐密度ヒューリスティックに加え、「書き込みあり + インラインロールチェックあり + `authorize()`/`Gate` 呼び出しが一切ない」ファイルを優先的に読むべきものとして報告するpriorityシグナルを実装している。`--audit-all` でリポジトリ全体を走査、`--stats` でトレンド計測用の件数のみを出力、指摘があればexit 1を返しCIでのゲートにも使える。
- 当初提案されていたスキーマ/権限DSLではなくこの形を採用した理由: ENリポジトリ側ですでにこのハーネスを使っている実プロジェクトを計測したところ、21個のControllerに対して103件の指摘（Controller内での `DB::transaction()` 呼び出しや、12個のPolicyクラスが存在するにもかかわらず手書きの `isAdmin()` / `abort(403)` 認可が行われている等）が見つかり、プローズによるルールだけでは境界を維持できないことが実証された一方、フルDSL＋コンパイラはドキュメント・ルールを配布するだけの本リポジトリには不釣り合いに大きい。詳細は `meta/adr/ADR-0011-domain-boundary-contract.md`（EN側のADR-0010に相当。本リポジトリでは0010番が別件のADRですでに使用されているため0011番として採番した）に記録した。
- 不変条件宣言ループ（`data-model.md` に宣言 → Redフェーズでのテストカバレッジ要求 → Gate 4承認）は、却下ではなく**保留（deferred）**とし、その理由・再検討条件をADR-0011に記録した。コストは変更のたびに発生する一方、恩恵はずっと後にしか現れないこと、Feature Testは振る舞いの存在は強制できてもレイヤーの遵守は強制できないことが理由である。
- パフォーマンスは設計上の制約として扱った。ファイルリストを1ファイルごとに `grep` でフィルタする初期案はWindows/Git Bashで300Controllerに対して15.8秒かかったが、1パスでのフィルタに変更した結果1.1秒（実プロジェクトの21Controllerでは0.59秒）に短縮された。

### Files touched

`meta/adr/ADR-0011-domain-boundary-contract.md`（新規）、`meta/adr/README.md`、`.claude/hooks/domain-boundary-check.sh`（新規）、`.claude/rules/10-laravel.md`、`.claude/rules/50-review.md`、`.claude/commands/review.md`、`PLAN.md`。

### Status

完了。Gate関連のテーブル（`.claude/rules/00-global.md`、`SETUP.md`、`AGENTS.md`）はGate条件自体に変更がないため意図的に変更していない。保留とした不変条件宣言ループは、ADR-0011に記載した再検討条件が満たされた時点で見直す。

## Split one-time Gate 0 setup steps out of CLAUDE.md into SETUP.md (2026-08-27)

### Decision

- Ported the SETUP.md split from the EN template repo (`ai-driven-development-setting-files-en`) into this JP repo. `CLAUDE.md`'s Gate 0 Step 1-4 section (frontend stack selection, ai-context fill-in, requirements docs, architecture design, TDD pipeline diagram) was moved verbatim (translated to Japanese) into a new top-level `SETUP.md`, read once at project kickoff. `CLAUDE.md` now only keeps a short pointer to it plus the steady-state per-session rules.
- Cross-references to `CLAUDE.md`'s Step 1-4 procedure were repointed to `SETUP.md` in `.claude/rules/00-global.md`, `.claude/rules/60-docs.md`, `meta/adr/ADR-0005-frontend-stack.md`, and `README.md`. `AGENTS.md` was left unchanged, matching the EN repo's treatment (it never duplicated the Step 1-4 procedure).
- Also added `.gitattributes` (`* text=auto`) and a `.gitignore` entry for `.claude/settings.local.json`, mirroring the EN repo's changes, to stop line-ending diff noise and personal local settings from being tracked.

### Files touched

`SETUP.md` (new), `CLAUDE.md`, `.claude/rules/00-global.md`, `.claude/rules/60-docs.md`, `meta/adr/ADR-0005-frontend-stack.md`, `README.md`, `.gitattributes` (new), `.gitignore`.

### Status

Completed. No open follow-ups.

## Separate template/harness ADRs from project ADRs (2026-08-15)

### Decision

- `docs/adr/` is reserved exclusively for the ADRs of the project built from this template. It now starts empty; the first project ADR should be `ADR-0001`.
- The 9 ADRs that document this template/harness's own design (ADR-0001 through ADR-0009) were moved to `meta/adr/`, a new top-level directory outside `docs/`. This keeps them out of any future "reset project docs" sweep of `docs/`, and out of the project's own ADR numbering sequence.
- All cross-references to these 9 files (in `CLAUDE.md`, `AGENTS.md`, `.claude/rules/`, `docs/ai-context/`, `docs/architecture/`, `docs/development/`) were repointed to `meta/adr/`. References to `docs/adr/` that describe creating a *new* project ADR (e.g. `/adr` command, `CLAUDE.md` Step 1a/3, Gate rules) were left unchanged.
- Added `docs/adr/README.md` and `meta/adr/README.md` explaining the split so it isn't rediscovered by accident later.

### Files touched

`meta/adr/ADR-0001` through `ADR-0009` (moved from `docs/adr/`), `docs/adr/README.md` (new), `meta/adr/README.md` (new), `README.md`, `CLAUDE.md`, `AGENTS.md`, `.claude/rules/00-global.md`, `.claude/rules/15-frontend.md`, `.claude/rules/30-testing.md`, `.claude/rules/31-e2e-testing.md`, `.claude/rules/50-review.md`, `docs/ai-context/common-commands.md`, `docs/ai-context/module-map.md`, `docs/development/ai-workflow.md`, `docs/architecture/authz-authn.md`.

### Status

Completed. No open follow-ups.

## Frontend stack selection process built into Gate 0 (2026-08-03)

### Decision

- `docs/adr/ADR-0005-frontend-stack.md` was changed from a fixed decision (Vue 3 + Inertia.js + Pinia for all projects) to a per-project selection framework within the PHP/Laravel ecosystem (Blade / Livewire / Vue+Inertia+Pinia / React+Inertia / SPA+API), with Vue+Inertia+Pinia kept as the default recommendation.
- The selection process is now an explicit part of Gate 0 (`CLAUDE.md` Step 1a/1b/1c): select stack → record a project ADR via `/adr` → rewrite `.claude/rules/15-frontend.md` for the chosen stack → reflect the result in `docs/ai-context/`.
- `.claude/rules/15-vue.md` was renamed to `.claude/rules/15-frontend.md` so the rule file path stays stable regardless of which stack is selected — projects choosing a non-default stack rewrite this file's contents instead of creating a new file and updating every cross-reference.
- Backend (Laravel + MySQL, ADR-0001/0002) and auth strategy (Sanctum + Policy/Gate, ADR-0003) remain fixed template decisions — out of scope for this flexibility.

### Files touched

`docs/adr/ADR-0005-frontend-stack.md`, `docs/adr/ADR-0006-e2e-testing-playwright.md`, `CLAUDE.md`, `AGENTS.md`, `README.md`, `.claude/rules/00-global.md`, `.claude/rules/15-frontend.md` (renamed from `15-vue.md`), `.claude/rules/30-testing.md`, `.claude/rules/50-review.md`, `.claude/rules/60-docs.md`, `.claude/agents/tdd-implementer.md`, `docs/ai-context/module-map.md`.

### Status

Completed. No open follow-ups.
