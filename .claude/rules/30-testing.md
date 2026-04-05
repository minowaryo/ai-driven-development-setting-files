# 30-testing.md — テスト方針

## テスト優先順位

1. **Feature Test（最優先）**: HTTPリクエスト〜レスポンスの統合テスト
2. **Unit Test**: 複雑なビジネスロジック・計算ロジック
3. **E2E Test**: クリティカルなユーザーフロー

## 基本ルール

- 変更には必ずFeature Testを追加する
- バグ修正時は再発防止テストを先に書く（TDD）
- テストはDBをモックしない（実DBを使う）
- Factoryを活用してテストデータを生成する

## 命名規則

```php
// Feature Test: 何をテストするか明確に
test('管理者はユーザー一覧を取得できる', function () { ... });
test('一般ユーザーはユーザー一覧にアクセスできない', function () { ... });
test('未認証ユーザーはログインページにリダイレクトされる', function () { ... });
```

## テスト構造（AAA パターン）

```php
test('example', function () {
    // Arrange: テストデータ・前提条件を準備
    $user = User::factory()->create();

    // Act: テスト対象の処理を実行
    $response = $this->actingAs($user)->get('/dashboard');

    // Assert: 期待する結果を検証
    $response->assertOk();
});
```

## 必ずテストすること

- [ ] 正常系（ハッピーパス）
- [ ] 認証・認可（未認証/権限なし）
- [ ] バリデーションエラー
- [ ] 境界値・エッジケース
- [ ] 削除・更新の副作用

## コマンド

```bash
# 全テスト実行
php artisan test

# 特定ファイルのみ
php artisan test tests/Feature/UserTest.php

# カバレッジ確認
php artisan test --coverage
```
