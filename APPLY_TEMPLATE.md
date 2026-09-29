# APPLY_TEMPLATE.md — 本テンプレートを対象リポジトリに適用する

> **このファイルを読むタイミング**: 本テンプレートのファイルを別のディレクトリへ*1つでも*コピーする前。
> コピーを行う人（通常は、対象リポジトリで動作し本テンプレートのパスを指定されたAIセッション）が読む。
> このファイルが扱うのは、ハーネスファイルを対象リポジトリへ*取り込む*方法（Phase 0〜3）だけである。
> Phase 4以降は、対象リポジトリ側にコピーされた `SETUP.md` が引き継ぐ。このファイル自体は対象リポジトリへ
> **コピーしない**（後述のclass X）。

## Step A0 — 対象リポジトリの状態からパスを選ぶ（何かをコピーする前に）

| 対象ディレクトリの状態 | パス |
|---|---|
| 空、または `.git/` のみ | **新規プロジェクトのパス** — 衝突しうるものがない。`README.md` の「使い始め方」に従い、本リポジトリをそのままテンプレートとして使い（clone / 「use as template」）、その後 `SETUP.md` に従う。このファイルの残りは適用しない。 |
| 既にファイルがある（アプリケーションコード、独自の `README.md`、設定など） | **既存ファイルありのパス** — 以下のPhase 0〜3に従い、その後、対象リポジトリの `SETUP.md` Step 0（Phase 4〜6）に引き継ぐ。 |

分岐点は2つあり、それぞれ別の問いに答える:

1. **Step A0（このファイル）** — *ファイルが衝突しうるか？* コピーの方法を決める。
2. **対象リポジトリの `SETUP.md` Step 0** — *動いているアプリケーションコードがあるか？* Gate 0〜3の
   ドキュメントをどう作るかを決める。ファイルはあるが実際のアプリケーションコードがない対象（例:
   `README.md` だけ）は、ここでは既存ファイルありのパスを通り、その後 `SETUP.md` Step 0で新規プロジェクト
   パスに振り分けられる。動いているコードがある対象は、既存コードベース導入パス
   （`/onboard-existing-codebase`）に振り分けられる。

## 原則（既存ファイルありのパス）

- **対象リポジトリに既に存在するファイルは決して上書きしない。** 同じパスの衝突は、定義済みのマージ
  （class C）か、止まって尋ねる（class E）のどちらかであり、黙って一方を選ぶことは決してない。
- **Phase 0〜3はハーネスファイルを追加するだけである。** アプリケーションコードには触れず、既存の
  対象ファイルへの変更として許されるのは、class Cの追記、`CLAUDE.md` / `AGENTS.md` への追記（class D）、
  そしてclass Eの衝突を解消する際に人間が明示的に選んだものだけである。
- **Phase 0〜3は止まらずに実行する。** 行う作業はすべて追加的で元に戻しやすい（新規ファイルと、class C
  ファイルへのマーク付き追記ブロック）ため、各Phaseは人間のチェックポイントではない。止まるのは人間の
  判断が実際に必要なときだけである：Phase 0の事前チェックが失敗した、class Eの衝突が見つかった、または
  Phase 3の検証が失敗した場合。それ以外はPhase 3まで終えてから最終報告を1回行う。Phase 4
  （`/onboard-existing-codebase`）をコピーのセッションに続けて実行しないこと。
- **コピー元は本テンプレートの `git ls-files` であり**、ディレクトリをそのまま再帰コピーしてはならない。
  これにより、追跡されていない実行時ファイルやマシン固有のファイル（class X）は、今後新しいものが増えても
  自動的に除外される。
- 手順は最終報告で終わる。コミットメッセージを提案するが、コミットやpushは決して行わない。

## ファイル分類

| Class | パス | 対象リポジトリでの扱い |
|---|---|---|
| **A** — そのままコピー | `git ls-files` のうち、R / C / D / X に挙げていないすべてのパス。現時点では: `.claude/agents/`、`.claude/commands/`、`.claude/hooks/`、`.claude/rules/`、`.claude/skills/`、`meta/adr/`、`docs/`（すべて）、`SETUP.md`、`GLOBAL_CLAUDE.md`、`.mcp.json` | 対象リポジトリにそのパスが存在しなければ、バイト単位でそのままコピーする。存在すれば → class E（ただし `.mcp.json` は → class C）。今後本テンプレートに追加されたファイルは自動的にclass Aに入る。コピーすべきでないものはXに挙げること。 |
| **R** — 別名でコピー | `README.md` → `README_harness.md` | 対象リポジトリの `README.md` には一切触れない。`README_harness.md` はハーネス概要の読み取り専用の参照用コピーであり、対象リポジトリのどのルールもそれを読んだり保守したりしない（対象リポジトリの `.claude/rules/60-docs.md` にある「`README.md`（ディレクトリツリー）」の行は、本テンプレートリポジトリ自身のREADMEを指しており、対象リポジトリのどちらのファイルでもない）。`README_harness.md` が既に存在すれば → class E。 |
| **C** — 追記マージ | `.gitignore`、`.gitattributes`、および対象リポジトリに既に `.mcp.json` がある場合のみ `.mcp.json` | 既存の行はすべて残す。本テンプレートのルール行のうち未記載のもの（行単位の完全一致で判定）を、マーク付きのブロック1つの中に追記する（形式は下記）——コメント行と空行はコピーしない。ブロックのヘッダーが既にここを参照しているためである。本テンプレートの `.gitignore` のうち、このリポジトリでしか意味を持たないエントリ——現時点では `dist/`——はスキップする。対象リポジトリでは、実際にコミットしているファイルを隠してしまう可能性があるためである。`.mcp.json` については、本テンプレートの `mcpServers` のエントリをキーとして追加する。既に存在するキー → class E。本テンプレートの行が既存のルールと*矛盾する*場合（例: 対象リポジトリが `*.sh` に別の `eol` を既に設定している）→ class E。 |
| **D** — 新規作成 | `CLAUDE.md`、`AGENTS.md`、`PLAN.md` | `CLAUDE.md`: 本テンプレートのファイルをコピーし、**Repository** の行を対象リポジトリの `git remote get-url origin`（リモートがなければ `[REPOSITORY_URL]`）に設定する。それ以外の `[...]` プレースホルダーはすべてPhase 4で埋めるため残しておく。対象リポジトリに既に `CLAUDE.md` がある場合は、それを残し、用意した内容（`# CLAUDE.md` のタイトルを除く）を末尾に、class Cと同じマーク付きブロックの中で追記する。本テンプレートと矛盾する指示（例: 既存ファイルが自律的なコミットを許可している）がないかをPhase 0で確認する。矛盾があれば → class E。`PLAN.md`: ヘッダーブロック（最初の `##` エントリより上のすべて）だけで作成する——その下のエントリは本テンプレート自身の履歴であり、対象リポジトリのものではない。`AGENTS.md`（Codexのエントリポイント）: 本テンプレートのファイルをコピーする。対象リポジトリに既にある場合は、`CLAUDE.md` と同じ方法で追記し、同じ矛盾チェックを行う。`PLAN.md` が既に存在すれば → class E。 |
| **X** — 決してコピーしない | `.git/`、`.claude/settings.local.json`、`dist/`（本テンプレートの `.gitignore` で無視しているビルド出力）、`APPLY_TEMPLATE.md`（このファイル）、および `git ls-files` に含まれないその他すべて | マシン固有の設定、テンプレート内部のビルド出力、テンプレート側の手順。単純な再帰コピー（`cp -r`）ではこれらも拾ってしまうが、`git ls-files` では拾わない。これらがなくてもハーネスには影響しない：Claude Codeが読み込むのは `CLAUDE.md`・`.claude/`・`.mcp.json` であり、`settings.local.json` は権限が承認されたときにClaude Code自身が作り直す。 |
| **E** — 衝突: 止まって尋ねる | 対象リポジトリに既に存在するclass A / R / Dのパス、およびclass Cで矛盾する行・キー | 止まる。人間に両方のバージョン（またはdiff）と選択肢——対象リポジトリの方を残す / テンプレートの方を採る / 手でマージする——を示し、選ばれたものだけを適用する。自分でどちらかを選んで解消することは決してしない。 |

class Cのブロック形式（`.gitignore` / `.gitattributes`）:

```
# --- AI-driven development harness (appended from template; see APPLY_TEMPLATE.md) ---
<only the template rule lines not already present in the target>
```

## Phase（既存ファイルありのパス）

すべてのコマンドは**対象**リポジトリのルートで実行し、`TPL` には本テンプレートのパスを設定する。
Bashのスニペットは、Windowsの場合Git BashまたはWSLが必要である（`.claude/hooks/` と同じ前提条件）。

### Phase 0 — 事前チェック（ファイルは書き込まない）

1. 対象リポジトリで `git status --short` が空であること。空でなければ止まり、先にコミットかstashをするよう
   人間に依頼する。ハーネスのdiffが無関係な作業と混ざらないようにするためである。
2. 本テンプレートでも `git -C "$TPL" status --short` が空であること——Phase 1は作業ツリーの内容をコピー
   するため、コミットされていないテンプレートの編集が対象リポジトリに漏れてしまう。適用するテンプレートの
   バージョンとして `git -C "$TPL" rev-parse --short HEAD` を記録する。
3. 対象リポジトリのデフォルトブランチを確認する。`.claude/hooks/review-score.sh` と
   `domain-boundary-check.sh` のデフォルトは `main` である。対象リポジトリが別の名前（例: `master`）を
   使っている場合は、それらを実行する際に `REVIEW_SCORE_BASE_BRANCH` / `DOMAIN_BOUNDARY_BASE_BRANCH` を
   設定する必要がある。この注記をPhase 3の引き継ぎ報告に含め、Phase 4で
   `docs/ai-context/common-commands.md` に記録されるようにする。
4. 衝突の一覧を作る:

   ```bash
   git -C "$TPL" ls-files | while read -r f; do [ -e "$f" ] && echo "EXISTS: $f"; done
   [ -e README_harness.md ] && echo "EXISTS: README_harness.md"
   ```

   `README.md`（class R）、`.gitignore` / `.gitattributes`（class C）、`.mcp.json`（class C）、
   `CLAUDE.md` / `AGENTS.md`（class Dの追記）がヒットするのは想定どおりである。
   **それ以外のヒットはすべてclass Eである**——既存の `CLAUDE.md` や `AGENTS.md` で見つかった矛盾と
   あわせて列挙する。
   対象リポジトリに既に `.claude/` がある場合は、既存のルールとコマンドも同様に確認する——両方が
   読み込まれるため、ファイル名が異なっていても本テンプレートとの矛盾はclass Eである。
5. class Eの一覧が空なら、そのままPhase 1に進む。空でなければ**止まる**：class Eの各項目を示して
   人間とすべて解消してから進む。

### Phase 1 — class AとRをコピーする

```bash
git -C "$TPL" ls-files \
  | grep -vxE 'README\.md|PLAN\.md|CLAUDE\.md|AGENTS\.md|\.gitignore|\.gitattributes|APPLY_TEMPLATE\.md' \
  | while read -r f; do
      [ -e "$f" ] && continue   # already exists: handled in Phase 0 (class E) or Phase 2 (class C)
      mkdir -p "$(dirname "$f")" && cp "$TPL/$f" "$f"
    done
cp "$TPL/README.md" README_harness.md
```

その後、Phase 0で人間が選んだclass Eの解消内容（上のループはそれらのパスをスキップしている）を
そのとおりに適用し、それ以上のことはしない。

確認: `git status --short` に表示されるのは `??` のエントリと、class Eの解消で人間が明示的に選んだ
`M` だけであること。一覧は最終報告用に控えておく。

### Phase 2 — class Cのマージとclass Dの作成

1. `.gitignore` と `.gitattributes` にclass Cのブロックを追記する（対象リポジトリに独自の
   `.mcp.json` があれば、そのキーもマージする）。
2. class Dの説明どおりに `CLAUDE.md` と `AGENTS.md` を作成（または既存のものに追記）し、`PLAN.md` を
   作成する。
3. 確認: 変更した既存ファイルそれぞれの `git diff` に、追記したブロック・追加したキーだけが含まれていること。
   diffは最終報告用に控えておく。

### Phase 3 — 検証してから引き継ぐ

1. `git status --short` — `??` のエントリと、class Cのファイル、追記した `CLAUDE.md` / `AGENTS.md`、
   class Eの解消で人間が変更を選んだパスの `M` だけであること。それ以外の `M` は何かが上書きされたことを
   意味する：止まって報告する。
2. class Aのすべてのファイルがテンプレートと同一であること（class Eの解消で対象リポジトリ側を残した、
   または手でマージしたパスが表示されるのは想定どおりである——それぞれがPhase 0の一覧にあることを確認する。
   それ以外は失敗である）:

   ```bash
   git -C "$TPL" ls-files \
     | grep -vxE 'README\.md|PLAN\.md|CLAUDE\.md|AGENTS\.md|\.gitignore|\.gitattributes|\.mcp\.json|APPLY_TEMPLATE\.md' \
     | while read -r f; do cmp -s "$TPL/$f" "$f" || echo "DIFFERS: $f"; done
   cmp -s "$TPL/README.md" README_harness.md || echo "DIFFERS: README_harness.md"
   ```

   出力なし = 合格。
3. コピーしたファイルが、対象リポジトリの `.gitignore`（またはグローバルなexcludesファイル）で黙って
   無視されていないこと——無視されたファイルはステップ1を通過してしまうが、決してコミットされない:

   ```bash
   { git -C "$TPL" ls-files \
       | grep -vxE 'README\.md|PLAN\.md|APPLY_TEMPLATE\.md'; \
     echo README_harness.md; echo PLAN.md; } \
     | git check-ignore --no-index --stdin
   ```

   出力なし = 合格（`docs/credentials/README.md` のように `!` ルールで再び含められたパスは、正しく
   報告されない）。ヒットした場合 → そのパスについて `git check-ignore -v` を再実行してルールを確認し、
   止まって尋ねる（修正箇所は対象リポジトリの無視ルールであり、人間が判断する）。
4. フックスクリプトがこの環境で動作すること（終了コード0または1は問題ない。シェルのエラーは問題である）:

   ```bash
   REVIEW_SCORE_BASE_BRANCH=<default-branch> bash .claude/hooks/review-score.sh
   ```

5. **最終報告**: Phase 1のファイル一覧、Phase 2のdiff、上記の結果、適用したテンプレートのバージョン
   （Phase 0のステップ2）、デフォルトブランチの注記（Phase 0のステップ3）。最後にコミットメッセージの
   提案だけを添える——例: `chore: apply AI-driven development harness template (<template version>)`。
   コミットはこの手順に含まれない。
6. **セッションはここで終える。** Phase 4は新しいClaude Codeセッションで始める。そうすることで、対象
   リポジトリの新しい `CLAUDE.md` と `.claude/` が最初から読み込まれる。

### Phase 4〜6 — 対象リポジトリで、その `SETUP.md` に従って続ける

ここでは繰り返さない。ここから先は、対象リポジトリの `SETUP.md` が唯一の正である。

| Phase | 内容 | 定義されている場所 |
|---|---|---|
| 4 | `/onboard-existing-codebase`（動いているコードがない対象の場合は新規プロジェクトパス） | `SETUP.md` Step 0 → 既存コードベース導入パス |
| 5 | 生成されたドキュメントのレビュー：「要確認」リストの解消が、Gate 0〜3の統合承認になる | `SETUP.md` 既存コードベース導入パスの「このパスにおけるGate 0〜3」 |
| 6 | `/tdd` による開発（機能/UCごとにGate 4） | `SETUP.md` Step 4 |

## 対象外

既にハーネスを導入したプロジェクトに、本テンプレートの*その後の*更新を取り込むのは別の作業
（ファイルごとに判断する選択的マージ）であり、この手順では扱わない。
