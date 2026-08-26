# 10-laravel.md — Laravel固有ルール

## アーキテクチャ方針

### Controller
- 薄く保つ（Fat Controller禁止）
- バリデーションは `FormRequest` に委譲
- ビジネスロジックは `Service` / `Action` に委譲
- 直接 `DB::` を呼ばない

### Service / Action
- 1クラス1責務を守る
- `Action` クラスは `execute()` メソッドに処理を集約
- トランザクションはServiceレイヤーで管理

### Model
- `$fillable` を明示する（`$guarded = []` 禁止）
- スコープはModelに定義する
- リレーションは積極的に定義する
- ビジネスロジックをModelに書かない

#### Actor Stamp（`created_by` / `updated_by` / `deleted_by`）

「誰が行った操作か」をレコードに記録する仕組みは**モデル単位のオプトイン**とする。`docs/product/use-cases.md` または `docs/architecture/data-model.md` で監査証跡が求められているモデルにのみ適用し、基底モデルやワイルドカードObserver経由で全モデルに一律適用しない。

- 共通トレイト（`app/Concerns/HasActorStamps.php`）を再利用し、Modelイベントからカラムを埋める。Controller/Serviceで手動セットしない
- マイグレーション規約: カラムごとにnullable FKを1つ、例 `foreignId('created_by')->nullable()->constrained('users')->nullOnDelete()`（コンソール実行・システム起因の書き込みには認証済みactorが存在しないためnullable）
- Model規約: `use HasActorStamps;` のみを記述する。3カラムを `$fillable` に追加しない（トレイト側がセットするため、mass-assignable にしてはいけない）
- **論理削除時の注意**: Eloquentの `runSoftDelete()` は独自クエリで `deleted_at` を書き込み、`save()` を経由しないためsave系のフックは発火しない。`deleting` イベント内で明示的なupdateクエリを使い `deleted_by` をセットし、`restoring` でクリアする
- トレイト自体は、fixture用モデル・テーブルに対する4パス（作成・更新・削除・復元）すべてをUnit Testでカバーする。Feature Testからの間接的なカバレッジに頼らない

### Authorization
- **Policy / Gate を必ず使う**（手動チェック禁止）
- `authorize()` をControllerで明示的に呼ぶ
- ロールチェックはMiddlewareまたはPolicyに集約

### FormRequest
- バリデーションルールはFormRequestに書く
- `authorize()` も適切に実装する

## 命名規則

| 対象 | 規則 | 例 |
|---|---|---|
| Controller | PascalCase + Controller | `UserController` |
| Service | PascalCase + Service | `UserRegistrationService` |
| Action | PascalCase + Action | `RegisterUserAction` |
| FormRequest | PascalCase + Request | `StoreUserRequest` |
| Policy | PascalCase + Policy | `UserPolicy` |
| Event | PascalCase（過去形） | `UserRegistered` |
| Job | PascalCase | `SendWelcomeEmail` |

## 禁止事項

- `DB::statement()` での生SQL（必要な場合はADRを書く）
- `$guarded = []`
- Controllerでのビジネスロジック
- N+1クエリ（`with()` で積極的にEager Loading）
