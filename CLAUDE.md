# CLAUDE.md

このリポジトリ (Base) で AI agent (Claude Code) が守るべき指針と、利用可能な workflow の索引。

## 実装規律 (Implementation Discipline)

このリポの上で生成・変更するあらゆるコードは以下4原則に従う:

- **DRY** — 3箇所目の重複が出るまでは抽象化しない (premature abstraction 禁止)
- **YAGNI** — 仮定の将来要件のためのコードを書かない
- **TDD** — red → green → refactor、垂直スライス。テストは「振る舞い」を検証し、実装詳細に依存しない
- **Frequent commits** — 機能単位で細かく commit。1 PR = 1 squashed commit に収まる粒度

## 開発フロー

```
issue-create                            Issue 起票 (工数見積込み)
   ↓
[ branch切る ]                          feature/<#>-<summary>
   ↓
grill-with-docs (任意)                  設計を詰める / CONTEXT.md / ADR 更新
   ↓
[ 実装 ]
   ├── tdd                              テスト駆動で実装
   ├── diagnose                         バグ調査
   └── improve-architecture             構造リファクタ
   ↓
commit                                  Conventional Commits + (issue#<N>)
   ↓
finishing-branch                        test verify → PR → squash merge → delete branch
```

## Skills (`.claude/skills/`)

各 skill は自然文の発言で**自動発火**する。`/skill-name` で明示起動も可能。

| skill | 発火トリガ例 |
|-------|-------------|
| `grill-with-docs` | 「設計を詰めたい」「grill で」「言語化したい」 |
| `issue-create` | 「Issue 作って」「Issue 化」 |
| `tdd` | 「TDD で」「テスト駆動」「red-green-refactor」 |
| `diagnose` | 「バグ調査」「動かない」「再現したい」 |
| `improve-architecture` | 「リファクタ」「アーキ改善」「構造を直したい」 |
| `commit` | 「コミットして」「commit」 |
| `finishing-branch` | 「PR 出して」「マージ準備」「ブランチ閉じて」 |

## ドキュメント構造

このリポ (および Base 派生プロジェクト) では、案件のドキュメントを2層で管理する。

### Tier 1 (常時メンテ — 全プロジェクトで意識)

| Doc | 役割 |
|-----|------|
| `ARCHITECTURE.md` | システム構造・モジュール関係・データフロー |
| `CONTEXT.md` | ドメイン用語集 |
| `docs/adr/` | 重要決定の記録 (lazy、3条件 hard-to-reverse / surprising / real-trade-off を満たすとき) |

### Tier 2 (条件付き — AI が signal で能動提案・lazy 作成)

| Doc | 検出 signal | 適用案件 |
|-----|------------|---------|
| `DESIGN.md` | UI / コンポーネント / レイアウト / visual taste の話題 | UI を持つ案件 |
| `BRAND.md` | ブランド / トーン / コンテンツ / コピーライティング の話題 | マーケ / コンテンツ案件 |
| `DEPLOYMENT.md` | デプロイ / リリース / 環境変数 / インフラ の話題 | デプロイがトリビアでない案件 |
| `DATA.md` | データソース / スキーマ / ETL / 集計 の話題 | データ案件 |
| `SECURITY.md` | 認証 / 機密 / 脆弱性 / コンプライアンス の話題 | 機密データ扱う案件 |
| `API.md` | API endpoint / 公開契約 / OpenAPI の話題 | API 公開案件 |

### 命名規則とlazy 作成

- **配置**: 大文字 + `.md` で **リポ root** に置く (matklad ARCHITECTURE.md convention に揃える)。案件特化 doc が肥大化したら `docs/` 配下への移動を検討
- **lazy 作成**: 該当 signal を検出するまで作らない。空のplaceholder は置かない
- **更新**: 関連話題が出るたび inline で更新する (まとめて後でやらない)
- grill-with-docs / improve-architecture は ARCHITECTURE.md と CONTEXT.md を自動更新する

## AI agent 行動規範

- コミットメッセージ・PR 本文に AI 生成の旨を記載しない (`🤖 Generated with...`、`Co-Authored-By: Claude...` 等は付与しない)
- main ブランチに直接 commit / push しない
- `git push --force`、`git reset --hard`、`git branch -D`、`git clean -fd` 等の破壊的操作は明示許可なく実行しない
- 「動くはず」「テスト通るはず」での commit / PR は禁止。実行確認した上で commit する

## 参照

- [CONTEXT.md](./CONTEXT.md) — 用語集
- [CONTRIBUTING.md](./CONTRIBUTING.md) — 規約 (人間向け)
- [README.md](./README.md) — Base とは何か、使い方
