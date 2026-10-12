# plan-archive.md

> Template-internal archive of this repository's own `PLAN.md` entries (newest first). Moved verbatim per `.claude/rules/60-docs.md`.
> Not copied into target projects (`APPLY_TEMPLATE.md` class X).
> Covers: 2026-08-03 – 2026-09-30 (through the 2026-09-28 skills-vs-commands / `/regenerate-traceability` entry, plus the 2026-09-28 ADR renumbering entry and the 2026-09-29 / 2026-09-30 Git workflow entries), except the on-hold 2026-08-19 entry and the 2026-09-28 third-party skill entry, which stay in `PLAN.md` (archived 2026-09-29, 2026-09-30, 2026-10-01, 2026-10-05, 2026-10-06, 2026-10-12).

## Gitワークフロー: `lite` プロファイル（デフォルト）と `standard` の併設、1行で切り替え（ENリポジトリからの移植） (2026-09-30)

### Decision

- ENリポジトリ（`C:\workspace\ai-driven-development-setting-files-en`、`1107126..cca4b36`）で実装済みの
  変更を本リポジトリに移植した。根拠は `meta/adr/ADR-0015-git-workflow.md` の2026-09-30更新注記に記録。
- プロファイルの切り替え: `.claude/rules/70-git.md` の `Profile: lite`（デフォルト）または `standard` の
  1行を編集する（ユーザーが自然文で依頼し、AIが編集・コミットする。AIが自発的に切り替えることはない）。
  違いは `docs/development/git-workflow.md` §0 プロファイル の表の行だけで、安全ルールを含むそれ以外は共通。
- `lite`: AIがブランチ名を決めて待たずに作成する。`/tdd` 1サイクルにつき1コミット。`/review` は機密パスに
  触れた場合のみ尋ねる（規模は情報として示す）。コミット・ファイル・スコア・区分・テストを示した計画への
  1回の承認で commit → merge → push を実行してよい——いずれかが失敗したら残りは行わない。
- `standard`: 2026-09-29 のルールセットそのまま。選択基準: 実データを扱う本番システム、または2人以上の
  並行開発なら `standard`、それ以外は `lite`。
- `docs/ai-context/common-commands.md` に「Gitで困ったとき」（状況 → AIへの依頼）を追加した。
- 読み込みコスト: 全ルールを `docs/development/git-workflow.md`（Git操作の前に読む）に移し、
  `.claude/rules/70-git.md` は19行の常時読み込みコア（プロファイル行 + 全ルールを読まなくても適用する
  安全ルール）にした。コアのコミットメッセージ規定は英語のみ（2026-09-30 の決定を維持）。すべての
  ポインタを `docs/development/git-workflow.md` §N <日本語見出し> に付け替えた。CLAUDE.md の
  「Read when relevant」行は追加しない（ENと同じく、コア自身がいつ読むかを示すため）。
- `.claude/hooks/review-score.sh` はコメントのみの変更のため、ENからそのままコピーした。

### Files touched

新規: `docs/development/git-workflow.md`。
変更: `.claude/rules/70-git.md`、`.claude/commands/commit.md`、`.claude/commands/tdd.md`、
`.claude/skills/prepare-merge/SKILL.md`、`.claude/hooks/review-score.sh`（ENからそのままコピー）、
`.claude/rules/00-global.md`、`.claude/rules/30-testing.md`、`.claude/rules/50-review.md`、
`.claude/rules/60-docs.md`、`.gitignore`、`AGENTS.md`、`APPLY_TEMPLATE.md`、`CLAUDE.md`、`README.md`、
`SETUP.md`、`docs/ai-context/common-commands.md`、`docs/development/ai-workflow.md`、
`docs/development/coding-standards.md`、`meta/adr/ADR-0015-git-workflow.md`、`PLAN.md`、
`meta/history/plan-archive.md`（300行超過のため最も古い完了エントリを原文のまま移動。保留中の
ADR-XXXX エントリは移動しない）。

### Status

実装済み（未コミット）。`feat/git-lite-profile` 上。次: 明示的な指示を受けてコミット・マージする。

## Gitワークフローのルール: ブランチ・コミット単位・権限・--no-ff マージ記録・マージ前チェック区分（ENリポジトリからの移植） (2026-09-29)

### Decision

- ENリポジトリ（`C:\workspace\ai-driven-development-setting-files-en`、ブランチ `feat/git-workflow-rules`）で
  実装済みの変更を本リポジトリに移植した。根拠・不採用案は `meta/adr/ADR-0015-git-workflow.md` に記録。
- 主なホスティングはGitLab（GitHubでも動くこと）。MR/PRプロセス・CI・ブランチ保護は見送り、
  ブランチ + コミット + `--no-ff` マージコミットだけで記録を残す。
- GitHub Flow風の短命ブランチ（`<type>/<issue-no>-<slug>`）。`main` への直接コミットはdocs/誤字修正のみ。
- コミット単位: 一文で言える・常にGreen・振る舞いの変更 / リファクタ / 整形を分ける。`/tdd` は
  Red + Green で1コミット、Refactorは別コミット、マイグレーションは単独コミット。
- 権限: AIのコミットは `/commit`（分割案 → 人間の承認1回）経由のみ。pushは明示的な指示時のみ（`ask`）、
  force pushは拒否。マージは新設の `prepare-merge` スキルが準備し、明示的な指示時のみ実行。
- マージコミットをMRの軽量な代替とする: gitのデフォルト件名 + 短い「なぜ」 + `Merge-Check:` /
  `Review:` / `Tests:` トレーラー。
- マージ前チェックは `review-score.sh` を再利用: `light`（< 10、テストのみ）/ `recommended`
  （10〜29、`/review` 推奨）/ `required`（≥ 30 または機密パスあり、`/review` 必須）。閾値は社内の
  Laravelプロジェクト4件の履歴と Google「Small CLs」/ SmartBear の知見で較正したTrial。
- worktreeは並行セッションの場合のみ。タスクごとのサブエージェントレビューループは行わない。
  本プロジェクトのルールはSuperpowersのGitスキルより優先する。
- `.claude/rules/70-git.md` がGitルールの唯一の記述場所であり、他のファイルは1行のポインタまで。
  §N の番号はENと共通、見出しは日本語（§1 ブランチ / §2 コミット単位 / §3 コミットメッセージ /
  §4 権限 / §5 マージ / §6 マージ前チェック / §7 並行セッションとworktree / §8 プラグインスキルに対する優先）。
- コミットメッセージの言語: 移植時は従来の `coding-standards.md` の方針（日本語または英語）を §3 に
  引き継いだが、2026-09-30 にユーザーの判断で **英語のみ** に統一した（ENと同じ。`GLOBAL_CLAUDE.md` とも一致）。
- 「PR」の表記は、PRプロセスが存在しないため「コミット」「マージ前」に置き換えた（`40-security.md` と
  `docs/credentials/README.md` の「PR説明」はEN同様に残す）。

### Files touched

新規: `.claude/rules/70-git.md`、`.claude/commands/commit.md`、`.claude/skills/prepare-merge/SKILL.md`、
`meta/adr/ADR-0015-git-workflow.md`、`.claude/settings.json`（ENからそのままコピー）、
`meta/tests/review-score.test.sh`（ENからそのままコピー——テンプレート内部用、`APPLY_TEMPLATE.md` のクラスX）。

変更: `.claude/hooks/review-score.sh`（ENからそのままコピー）、`.claude/commands/review.md`、
`.claude/commands/tdd.md`、`.claude/rules/00-global.md`、`.claude/rules/30-testing.md`、
`.claude/rules/50-review.md`、`.claude/rules/60-docs.md`、`.gitignore`、`AGENTS.md`、`APPLY_TEMPLATE.md`、
`CLAUDE.md`、`README.md`、`docs/ai-context/common-commands.md`、`docs/development/ai-workflow.md`、
`docs/development/coding-standards.md`、`docs/development/review-checklist.md`、
`docs/product/org-permission-philosophy.md`、`docs/product/user-guide.md`、
`docs/security/secrets-handling.md`、`meta/adr/ADR-0009-review-escalation-mechanism.md`、
`meta/adr/README.md`、`PLAN.md`。

### Status

完了。`feat/git-workflow-rules` でコミットし、`/review`（強化レベル）の指摘を修正したうえで、
`prepare-merge` により `main` へ `--no-ff` でマージ。`bash meta/tests/review-score.test.sh` → 27/27。
テンプレートからコピーした新規プロジェクトも `SETUP.md`「Step 1 の前に」で `PLAN.md` をまっさらにし、
`meta/tests/`・`meta/history/`・`APPLY_TEMPLATE.md` を削除する。フォローアップ: Trial の閾値を実運用後に見直す（ADR-0015）。
あわせて、テンプレート自身の PLAN.md のアーカイブ先を `meta/history/plan-archive.md`（class X、導入先に
コピーしない）と定め、300行超過のため 2026-08-03 / 2026-08-15 の完了エントリを原文のまま移動した
（`.claude/rules/60-docs.md`、`APPLY_TEMPLATE.md`、`README.md` も更新）。
`APPLY_TEMPLATE.md` の class D で、対象リポジトリの `PLAN.md` はタイトル行のみのまっさらな状態で作成するよう変更
（テンプレート側の冒頭注記が漏れないように）。

## meta/adr のADR番号をENリポジトリに完全一致させる繰り下げ (2026-09-28)

### Decision

- 直前の3件の移植作業により、本リポジトリのADR番号がENリポジトリと1つずつズレていること
  （EN 0010〜0014 = JA 0011〜0015）が判明した。ズレの原因はJA側のADR-0010が、保留中の
  スキル化基準ドラフット（`ADR-XXXX-skillification-criteria.md`）のために空けられていた
  ことにある——ただしそのドラフット自身が「番号を恒久的に予約しない」方針を明言しているため
  （番号を確定させず `ADR-XXXX` のまま保留中）、0010番は実質的に空いていた。
- そこで `meta/adr/ADR-0011`〜`ADR-0015` の5ファイルを `ADR-0010`〜`ADR-0014` へ一つずつ
  繰り下げ、ENリポジトリの番号と完全一致させた:
  - `ADR-0011-domain-boundary-contract.md` → `ADR-0010-...`
  - `ADR-0012-existing-codebase-adoption.md` → `ADR-0011-...`
  - `ADR-0013-skills-vs-commands.md` → `ADR-0012-...`
  - `ADR-0014-third-party-skill-adoption-trial.md` → `ADR-0013-...`
  - `ADR-0015-third-party-integrations-deferred.md` → `ADR-0014-...`
- ファイル本体・タイトル行・相互参照は、`ADR-0011→0010→0011→0012→0013→0014`という
  プレースホルダー経由の一括置換（循環置換によるカスケード事故を避けるため）で更新した。
  対象は現在参照されている全ファイル（`.claude/rules/`・`.claude/commands/`・`.claude/skills/`・
  `.claude/hooks/domain-boundary-check.sh`・`CLAUDE.md`・`AGENTS.md`・`README.md`・`SETUP.md`・
  `docs/ai-context/common-commands.md`・`docs/development/ai-workflow.md`・`meta/adr/README.md`・
  ADRファイル自身の相互参照）。
- **`PLAN.md` の古い履歴エントリ（2026-09-15以前）はあえて書き換えていない**——本ファイル自身の
  既存の方針（「過去のPLAN.mdエントリは書かれた時点のファイル構成を記述する歴史的記録であり、
  書き換えない」）に従った。そのため古いエントリの一部は、今となっては存在しないファイルパス
  （例: 旧`ADR-0012-existing-codebase-adoption.md`）を参照したままになる——これは既知・許容
  済みの非対称性である。一方、**今回のセッションで直前に書いたばかりの3エントリ**（このエントリの
  直後に続く3件）は歴史的記録として固定する前だったため、今回の繰り下げに合わせて番号を更新した。
- `meta/adr/README.md` のADR一覧では、`ADR-XXXX`（スキル化基準ドラフット）の行を、
  「0010番の空き枠」の位置から一覧の末尾（0010〜0014がすべて埋まった後）に移動した——
  再開時には次の空き番号（0015以降）を使うことになる、というドラフット自身の方針とも整合する。

### Files touched

`meta/adr/ADR-0010-domain-boundary-contract.md`（旧ADR-0011からリネーム）、
`meta/adr/ADR-0011-existing-codebase-adoption.md`（旧ADR-0012からリネーム）、
`meta/adr/ADR-0012-skills-vs-commands.md`（旧ADR-0013からリネーム）、
`meta/adr/ADR-0013-third-party-skill-adoption-trial.md`（旧ADR-0014からリネーム）、
`meta/adr/ADR-0014-third-party-integrations-deferred.md`（旧ADR-0015からリネーム）、
`meta/adr/README.md`、`meta/adr/ADR-0004-ai-development-policy.md`、
`.claude/commands/adr.md`、`.claude/commands/onboard-existing-codebase.md`、
`.claude/commands/review.md`、`.claude/hooks/domain-boundary-check.sh`、
`.claude/rules/00-global.md`、`.claude/rules/10-laravel.md`、`.claude/rules/30-testing.md`、
`.claude/rules/60-docs.md`、`.claude/skills/grill-me/SKILL.md`、
`.claude/skills/systematic-debugging/SKILL.md`、
`.claude/skills/verification-before-completion/SKILL.md`、`AGENTS.md`、
`docs/ai-context/common-commands.md`、`docs/development/ai-workflow.md`、`README.md`、
`SETUP.md`、`PLAN.md`（本ファイル冒頭の直前3エントリのみ）。

### Status

完了。全ての相互参照・ファイル名・タイトル行の整合性を機械的に検証済み（ENリポジトリの
`meta/adr/` ファイル一覧と、`ADR-XXXX-skillification-criteria.md` を除いて完全一致）。
フォローアップなし。

## スキルとコマンドの使い分け基準 + /regenerate-traceability の追加（ENリポジトリからの移植） (2026-09-28)

### Decision

- ENリポジトリには存在するが本リポジトリにはまだなかった「スキル」という仕組み自体
  （`.claude/skills/` ディレクトリ、AIが自己判断で発動できるエントリポイントという概念）を移植した。
  これは今回の主目的（サードパーティ製スキル概念の導入）を行う前提として必要だったため、
  先にキャッチアップした。
- `meta/adr/ADR-0012-skills-vs-commands.md`（ENリポジトリの `ADR-0011` に相当。番号が異なるのは、
  導入した時点で本リポジトリ側の既存コードベース導入パスが `ADR-0012` を使用済みだったためで、
  後日ADR-0011〜0015の付け番をEN版に合わせて1つずつ繰り下げ、最終的に一致させた）に、
  スキル/コマンドの判断基準を記録した:「実行し忘れる」ことが失敗モードならスキル、
  「タイミングを誤って実行する」ことが失敗モードならコマンド。既存6コマンドはいずれもコマンド側の
  ままとした（移行のコストに見合う機能的な利点がないため）。
- この基準の最初の適用例として `.claude/skills/regenerate-traceability/SKILL.md` を新規追加した——
  `docs/rcid/traceability-matrix.md` の「マトリクス」表（「変更追跡」表は対象外）を、
  `use-cases.md` と実際のコード・テストから再生成するスキル。JA版の見出し表記
  （「マトリクス」「変更追跡」「最終再生成日:」）に合わせて内容を調整した。
- 単体エクスポート（`dist/skills/<name>/SKILL.md`、`.gitignore` 対象）の慣行もADRに記録したが、
  実際のエクスポートファイル自体は生成していない——ビルド成果物であり、共有したくなった時点で
  再生成するものであるため。

### Files touched

`meta/adr/ADR-0012-skills-vs-commands.md`（新規）、
`.claude/skills/regenerate-traceability/SKILL.md`（新規）、`.gitignore`、
`docs/ai-context/common-commands.md`、`README.md`、`meta/adr/README.md`。

### Status

完了。フォローアップなし。

## review-score / domain-boundary フックの堅牢化とドキュメント整合性の修正（ENリポジトリからの移植） (2026-09-28)

### Decision

- ENリポジトリのコミット `e076b6a`（"fix: harden review-score/domain-boundary hooks and doc
  consistency"）で修正済みだった内容を本リポジトリに移植した。これらは本リポジトリのフック側には
  未反映のバグ修正だった：
  - `review-score.sh` / `domain-boundary-check.sh` の両方に、サブディレクトリから実行しても
    動作するようリポジトリルートへ `cd` する処理を追加した
  - 両スクリプトとも、コミット済みの差分だけでなく**未コミット・未追跡の変更もスコアリング/監査対象に
    含める**よう変更した——`/tdd` 直後に `/review` を実行するとスコアがゼロになっていた問題を修正
  - ベースブランチがローカルに存在しない場合、`origin/<base>` へフォールバックする処理を追加した
  - `.claude/rules/50-review.md` と `meta/adr/ADR-0009-review-escalation-mechanism.md` にあった
    ドキュメントとコードの不一致（ドキュメントは「閾値を超えたら」、コードは `>=`）を修正し、
    両ファイルの文言を「閾値以上」に統一した
  - `.claude/commands/onboard-existing-codebase.md` にあった壊れた相互参照（存在しない見出し名
    「Claude Code組み込みの`/init`との関係」を参照していた）を、`SETUP.md` に実在する太字の
    注記文言を参照するよう修正した
  - `README.md` に前提条件（Bash必須、Windows Git Bash/WSLの案内）セクションを追加した
  - あわせて `SETUP.md` の各Stepに、コマンド実行例とその結果の説明（`/onboard-existing-codebase`・
    `/adr`・`/generate-mock UC-006`・`/tdd UC-006 ...`）を追加し、ENリポジトリの具体性に合わせた

### Files touched

`.claude/hooks/review-score.sh`、`.claude/hooks/domain-boundary-check.sh`、
`.claude/commands/review.md`、`.claude/rules/50-review.md`、
`meta/adr/ADR-0009-review-escalation-mechanism.md`、
`.claude/commands/onboard-existing-codebase.md`、`README.md`、`SETUP.md`。

### Status

完了。フォローアップなし。

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
