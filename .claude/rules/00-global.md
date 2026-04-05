# 00-global.md — 全体方針

## 開発ワークフロー

必ずこの順序で進める:

```
Explore → Plan → Implement → Test
```

1. **Explore**: 関連コード・ドキュメントを読み、現状を理解する
2. **Plan**: 変更の影響範囲・リスクを整理し、実装方針を固める
3. **Implement**: 計画に従って実装する（脱線しない）
4. **Test**: Feature Testを追加・実行して動作を確認する

## 絶対禁止

- コード先行（ドキュメント未確認での実装）
- 大規模変更の一括コミット
- テストなし実装（バグ修正には再発防止テストを必ず追加）
- 関係のないファイルの編集

## AI開発パイプライン

```
requirements.md（人間定義）
      ↓
use-cases.md（人間レビュー ← 品質ゲート）
      ↓
data-model.md / openapi.yaml
      ↓
AI code generation
      ↓
AI test generation
```

## 品質ゲート

- Gate 1: `docs/product/requirements.md` 完了
- Gate 2（最重要）: `docs/product/use-cases.md` 承認済み
- Gate 3: `docs/architecture/data-model.md` 確認済み

## 変更時の確認リスト

- [ ] 関連ADRを確認したか
- [ ] use-casesに変更はないか
- [ ] マイグレーション計画はあるか（DB変更の場合）
- [ ] テストを追加したか
- [ ] ドキュメントを更新したか
