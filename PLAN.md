# PLAN.md

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
