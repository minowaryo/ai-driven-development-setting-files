# PLAN.md

> 300行未満を維持する（`.claude/rules/60-docs.md`）。アーカイブ済み: 2026-08-03 – 2026-09-28 のスキル/コマンド使い分け基準エントリまで（保留中の 2026-08-19 エントリを除く）→ `meta/history/plan-archive.md`（2026-09-29、2026-09-30、2026-10-01、2026-10-05）

## Domain Boundaryチェック: 検出精度の改善 + 専用テスト、prepare-merge での毎回実行（ENリポジトリからの移植） (2026-10-05)

### Decision

- EN `84f26bc`（精度改善）と `7f3c107`（prepare-merge で実行）を移植。JP版のスクリプトと `review-score.test.sh` は
  EN版の変更前と同一だったため、`.claude/hooks/domain-boundary-check.sh` と `meta/tests/domain-boundary-check.test.sh`
  （新規）はそのままコピーする。理由・残っている課題は ADR-0010 の 2026-10-03 更新メモ2件。
- 文書は日本語に訳して反映: ADR-0010・ADR-0015 の更新メモ、`docs/development/git-workflow.md` §5/§6、
  `.claude/skills/prepare-merge/SKILL.md` ステップ2〜5、`.claude/rules/10-laravel.md`、`README.md`。
- 対象外: EN版の Loop Engineering（段階4で移植予定）と spec-lint など（EN `9406ae7`）。

### Status

ブランチ `feat/domain-boundary-port` で移植済み。テスト: `domain-boundary-check.test.sh` 31/31、`review-score.test.sh` 27/27。
300行を超えたため、完了済みの 2026-09-28 スキル/コマンド使い分け基準エントリを `meta/history/plan-archive.md` に移した。

## ADR-0016番号確保: Loop Engineering 段階1（EN版は番号衝突回避のみ） (2026-10-03)

### Decision

- EN版 ADR-0016「Loop Engineering ロードマップと段階1（機械によるTDDの強制）」は EN版で Trial として承認済み。
  JP版への本格移植は EN版の段階4（`meta/design/loop-stage4-design.md` の Porting plan）で行う方針のため、
  今回は番号衝突を避けるべく `meta/adr/ADR-0016-loop-engineering-stage1.md` を仮ファイルとして作成し、
  `meta/adr/README.md` に1行追加した。内容はEN版へのポインタのみ（Trial中・JP未適用）。
- 保留中のADR-XXXX（skillification-criteria）の草案および関連するPLAN/README行には触れていない。

### Status

番号確保のみ完了（未コミット）。`docs/reserve-adr-0016` ブランチ上。次: ユーザー承認後にコミットする。

## セッションごとの読み込み量削減: 必要な時だけ読む内容を常時読み込みファイルから移動（ENリポジトリからの移植） (2026-10-01)

### Decision

- ENリポジトリ（`C:\workspace\ai-driven-development-setting-files-en`、`cca4b36..293cba9`）で実装済みの
  変更を本リポジトリに移植した。Git分割と同じコア/詳細パターンを、特定の時点でしか必要ない内容にのみ適用:
  `.claude/rules/31-e2e-testing.md` → `docs/development/e2e-testing.md`（`/generate-e2e-test` が読む）、
  `50-review.md` の本文 → `docs/development/review-guidelines.md`（`/review` が読む。4項目のコアを残す）、
  `60-docs.md` の PLAN アーカイブ手順 → `docs/development/plan-archiving.md`（上限とアーカイブ先は残す）、
  ADRテンプレート → `.claude/commands/adr.md`（唯一の正本）へのポインタ、「Gitで困ったとき」の表 →
  `docs/development/git-troubleshooting.md`（毎セッション読む `common-commands.md` から外す）。
- 移動はすべて本リポジトリの日本語本文を原文のまま（diffで確認）。調整したのはタイトル・「いつ読むか」の
  1行注記・自己参照のみ。
- 結果（文字数）: 常時読み込みの `CLAUDE.md` + `.claude/rules/*` 33,144 → 26,800、読み込み必須の
  ai-context 4ファイルを含めると 41,614 → 34,650。
- 見送り（ENと同じ）: `10-laravel` / `15-frontend` / `20-mysql` の `paths:` スコープ化——個人での試用後に判断する。

### Files touched

`CLAUDE.md`、`README.md`、`.claude/rules/30-testing.md`、`.claude/rules/50-review.md`、
`.claude/rules/60-docs.md`、`.claude/commands/review.md`、`.claude/commands/generate-e2e-test.md`、
`.claude/skills/prepare-merge/SKILL.md`、`docs/ai-context/common-commands.md`、
`docs/development/{e2e-testing,review-guidelines,git-troubleshooting,plan-archiving}.md`（新規または移動）、
`docs/development/git-workflow.md`、`docs/development/review-checklist.md`、
`docs/development/testing-strategy.md`、`meta/adr/ADR-0007-tdd-enforcement-probity.md`、
`meta/adr/ADR-0009-review-escalation-mechanism.md`、`PLAN.md`、`meta/history/plan-archive.md`
（2026-09-15 と 2026-09-28 のフック堅牢化エントリを原文のまま移動。保留中の ADR-XXXX エントリは移動しない）。

### Status

実装済み（未コミット）。`feat/token-reduction` 上。次: 明示的な指示を受けてコミット・マージする。

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

## サードパーティ製スキル概念の自社導入（Trial）+ 見送り記録（ENリポジトリからの移植） (2026-09-28)

### Decision

- ENリポジトリで実施済みの「Superpowers比較を踏まえたサードパーティ製スキル/プラグイン導入検討」を
  本リポジトリに移植した。ENリポジトリ側では2回の調査パス（本リポジトリ自身の `.claude/` 構成の
  棚卸しと、Superpowers・Laravel Boost・cc-sdd・`mattpocock/skills`・hookifyの実態確認）を経て
  判断されており、その結論を翻訳・番号を付け替えて移植した。
- **自社流用、まとめて一度に導入、Trialと明記**（`meta/adr/ADR-0013-third-party-skill-adoption-trial.md`）:
  新規スキル `.claude/skills/systematic-debugging/SKILL.md`（不明瞭なバグへの再現・切り分け規律）と
  `.claude/skills/verification-before-completion/SKILL.md`（このターンで実際に実行するまで「完了」と
  言わない）、`.claude/rules/30-testing.md` への新規「テストの質に関するヒューリスティクス」節
  （検証対象の振る舞いをモックしない、期待値を実装から逆算しない、意図した修正を戻しても失敗するか
  サニティチェックする）、そして新規スキル `.claude/skills/grill-me/SKILL.md`（`mattpocock/skills` から
  翻案・出典明記した、要件定義の一問一答インタビュー）。いずれもインストールするプラグインではない——
  SuperpowersのSessionStart強制注入（`<EXTREMELY_IMPORTANT>` タグで約1,300トークン。GitHub Issue
  #1480/#1456/#2377で検証済み）とTDDの非強制（Issue #384/#2372）が実際にリスクであることを検証した
  上で、その根底にあるアイデアだけを自社で書き直した。ユーザーは4項目を段階導入せずまとめて一度に
  導入することを明示的に選択した——3項目はAI自身の内部規律を厳しくするだけであり、`grill-me` だけが
  人間とのやり取りのパターンを変えるため、その1点だけをロールアウト追跡表で個別に注視する。
  `ADR-0013` はADR Statusに新しい値「Trial」を導入し、`.claude/commands/adr.md` と
  `.claude/rules/60-docs.md` のテンプレートにも反映した。
- **検討したが見送り、記録のみで機能変更なし**（`meta/adr/ADR-0014-third-party-integrations-deferred.md`）:
  Laravel Boost（`laravel/boost`）——却下ではなく見送り。`php artisan boost:install` が
  `CLAUDE.md`/`AGENTS.md` を上書きしてしまうため。導入する場合はMCPサーバーのみを手動登録し、
  本テンプレートには絶対にフルインストーラを実行しないこと。cc-sdd（`gotalab/cc-sdd`）——本ハーネス
  自身のGate 0〜3パイプラインと重複するため却下。hookify（Anthropic公式プラグイン）——見送り・様子見。
  `ADR-0010` のスクリプト実行方式という意図的な選択があるため、実フック化の検討は別枠で行う。
  Superpowers本体（プラグイン丸ごとの導入）——上記2つの検証済みリスクを理由に却下。

### Files touched

`meta/adr/ADR-0013-third-party-skill-adoption-trial.md`（新規）、
`meta/adr/ADR-0014-third-party-integrations-deferred.md`（新規）、
`.claude/skills/systematic-debugging/SKILL.md`（新規）、
`.claude/skills/verification-before-completion/SKILL.md`（新規）、
`.claude/skills/grill-me/SKILL.md`（新規）、`.claude/rules/30-testing.md`、
`.claude/rules/00-global.md`、`CLAUDE.md`、`docs/ai-context/common-commands.md`、
`README.md`、`meta/adr/README.md`、`.claude/commands/adr.md`、`.claude/rules/60-docs.md`。

### Status

実装済み。未コミット——明示的な指示を待つ。フォローアップ: `ADR-0013` のロールアウト追跡表を、
バッチをしばらく使ってから見直す——Acceptedへ昇格させるか、個別にロールバックするか判断する
（`grill-me` の人間側の摩擦を最優先で観察する）。

## [ON HOLD] ADR-XXXX: skill-ification criteria and detection mechanism (2026-08-19)

### Decision

- Drafted `meta/adr/ADR-XXXX-skillification-criteria.md` (Status: Proposed) defining a checklist for when a repeated procedure should become a slash command / sub-agent, and deciding against full automatic detection (no cross-session log exists to measure "frequency" objectively) in favor of a lightweight AI self-check at `/review` time that only ever *suggests*, never auto-creates.
- Paused before finalizing: the checklist was written speculatively, without having actually lived through the same manual procedure 2-3 times first. Decided to hold off on Accepted status, on assigning a real ADR number, and on any wiring (e.g. into `.claude/rules/50-review.md`) until there's real repeated-procedure experience to check the criteria against. Left as `ADR-XXXX` rather than a reserved number, since it's uncommitted and the resumption timing is unknown — this avoids permanently reserving a number for an indefinitely-paused draft.

### Files touched (uncommitted, left in working tree — not stashed)

`meta/adr/ADR-XXXX-skillification-criteria.md` (new, unnumbered pending resumption), `meta/adr/README.md` (added ADR-XXXX row).

### Status

On hold. Next action: resume once 2-3 real instances of a candidate repeated procedure have been observed, then revisit the checklist against that experience before moving Status to Accepted and wiring it into `.claude/rules/50-review.md`.
