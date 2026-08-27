# SETUP.md

## ⚠️ プロジェクト開始前の必須手順（Gate 0）

> **このファイルを読むタイミング**: このリポジトリをクローンした直後、プロジェクト開始時に一度だけ。
> `CLAUDE.md` の毎セッション読み込みリストには含まれない — Step 1〜3が完了しGate 0〜3を通過した後は、
> 日常作業においてこのファイルを再読する必要はない（Gate 4は機能・UC単位で繰り返されるが、
> そのサイクルは `.claude/rules/30-testing.md` と `/tdd` コマンドが駆動するものであり、
> このファイルの再読とは無関係）。

このリポジトリをクローンしたら、コードに触れる前に以下を順番に埋めること。
AIはこれらのファイルが埋まっていない状態では正確な支援ができない。

### Step 1 — フロントエンド技術選定 → ai-context を埋める（最初に必ずやること）

**1a. フロントエンド技術選定**

- `meta/adr/ADR-0005-frontend-stack.md` の選定基準・比較表を確認し、このプロジェクトのフロントエンドスタックを決定する
- `/adr` コマンドで選定結果を `docs/adr/ADR-XXXX-frontend-stack-selection.md` として記録する（デフォルト推奨〔Vue 3 + Inertia.js + Pinia〕以外を選ぶ場合、または複数候補で迷った場合は理由と却下案を明記する）

**1b. 選定確定後のルールファイル反映**

- `.claude/rules/15-frontend.md` の内容を選定結果に合わせて書き換える（Vue 3 + Inertia.js + Pinia を選定した場合はデフォルト内容のまま利用可）
- `docs/ai-context/module-map.md` の Frontend セクションを実際のディレクトリ構成に書き換える（例示のままにしない）

**1c. ai-context を埋める**

> 作成にあたっては `docs/original-docs/` に一次資料（要件メモ・画面スケッチ等）を置いてから参照すること。
> Step 1 完了後は `docs/original-docs/` をデフォルト参照先としない。
> `project-summary.md` の Frontend 行には 1a の選定結果を転記する。

| ファイル | 内容 | 優先度 |
|---|---|---|
| `docs/ai-context/project-summary.md` | プロジェクト全体の概要・目的・技術スタック | 必須 |
| `docs/ai-context/glossary.md` | プロジェクト固有の用語・略語 | 必須 |
| `docs/ai-context/module-map.md` | ディレクトリ構成と各モジュールの責務 | 必須 |
| `docs/ai-context/do-not-touch.md` | AIが変更してはいけない領域・ファイル | 必須 |
| `docs/ai-context/common-commands.md` | よく使うコマンド（migrate / test / lint 等） | 推奨 |

### Step 2 — 要件定義ドキュメントを作成する

```
docs/product/requirements.md        ← ビジネスチーム・BAが作成
    ↓ Gate 1: レビュアー承認
docs/product/use-cases.md           ← ビジネスチーム・BAが作成（AIによる叩き台生成可）
    ↓
docs/product/mockups/               ← AIによる叩き台生成可（/generate-mock コマンド利用）
    ビジネス側レビュー → フィードバックを use-cases.md に反映
    ↓ Gate 2: レビュアー最終承認 ★ここを通過するまでコード生成禁止
docs/product/acceptance-criteria.md ← AIによる叩き台生成可
```

> **モック作成タイミングの原則**: モックはGate 1通過後〜Gate 2の間に作成する。
> ビジネス側との要件認識合わせが目的であり、Gate 3（データモデル承認）を待つ必要はない。
> モックフィードバックをuse-cases.mdに反映してからGate 2承認を行う。

### Step 3 — アーキテクチャ設計

```
docs/architecture/data-model.md  ← 開発者が作成（AIによる叩き台生成可）
docs/architecture/overview.md    ← 開発者が作成
docs/adr/ADR-xxxx-[title].md     ← 技術選定の都度作成
    ↓ Gate 3: レビュアー承認
```

### Step 4 — コード生成・実装（Gate 2・3 通過後のみ）

実装は `/tdd` コマンドで **TDD（Red → Green → Refactor）** で進める。

```
Red → [Gate 4: テストケース承認 ★実装(Green)着手禁止] → Green → Refactor → /review
```

> Gate 4 は Gate 0〜3（プロジェクトで1度だけ通過）と異なり、機能・UC単位でTDDサイクルのたびに繰り返す。
> フェーズごとの手順・サブエージェント構成・スキル実行タイミングは `.claude/rules/30-testing.md` を参照。
