# CLAUDE.md

このリポジトリで AI agent (Claude Code) が守るべき指針と、利用可能な workflow の索引。

## 実装規律 (Implementation Discipline)

このリポの上で生成・変更するあらゆるコードは以下4原則に従う:

- **DRY** — 3箇所目の重複が出るまでは抽象化しない (premature abstraction 禁止)
- **YAGNI** — 仮定の将来要件のためのコードを書かない
- **TDD** — red → green → refactor、垂直スライス。テストは「振る舞い」を検証し、実装詳細に依存しない
- **Frequent commits** — 機能単位で細かく commit。1 PR = 1 squashed commit に収まる粒度

## 開発フロー

```
[ アイデア / 課題 ]                     1-2 文の発想から
   ↓
grill-with-docs                         設計判断 / 新概念 / 構造影響
                                        のいずれかがあれば必須
                                        CONTEXT/ARCHITECTURE/ADR を inline 更新
                                        → trivial fix なら skip 可
   ↓
issue-create                            ISSUE_TEMPLATE 全項目を埋めて起票
                                        (grill した場合はその結果を転記)
   ↓
[ branch切る ]                          feature/<#>-<summary>
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

## ドキュメント Catalog

案件のドキュメントは **Catalog (辞書)** として管理する。階層 (常時/条件付き) は持たず、会話中に signal が surface したら、AI agent は Catalog を辞書として引き、該当 doc の作成/更新を能動提案する。

| Doc | 主問い | 領域 | Signal シナリオ |
|-----|-------|------|----------------|
| `AUDIENCE.md` | 誰に向けた案件か | 横断 | ターゲット顧客・ユーザー像・読み手像を議論しているとき |
| `STAKEHOLDERS.md` | 誰が関与・承認・意思決定するか | 横断 | 依頼主・承認者・関係チームの役割を整理しているとき |
| `BRIEF.md` | 何の案件か (目的・スコープ・成功基準) | 横断 | kickoff、案件の目的・KPI・成功基準を言語化するとき |
| `CONTEXT.md` | 何という概念を扱うか (ドメイン用語集) | 横断 | 新しい概念名が登場、既存用語の再定義、命名議論をしているとき |
| `ARCHITECTURE.md` | 何で構成されているか (構造・モジュール・データフロー) | 開発 | 構造・コンポーネント関係・データフロー・拡張点・リファクタの話題 |
| `API.md` | 何を公開するか (endpoint・契約) | 開発 | API endpoint・OpenAPI・公開契約の議論 |
| `DATA.md` | 何のデータを扱うか (スキーマ・ETL) | リサーチ/開発 | データソース・スキーマ・ETL・集計の議論 |
| `CONTENT.md` | 何のコンテンツを作るか | クリエイティブ | 記事・素材・編集ワークフローの議論 |
| `DESIGN.md` | 何を見せるか (UI 構造・visual taste) | クリエイティブ | UI・コンポーネント・レイアウト・visual taste の議論 |
| `docs/adr/` | なぜこの決定にしたか | 横断 | アーキ決定・採否判断・「なぜこっちにした」を残すべきとき (hard-to-reverse / surprising / real-trade-off の 3 条件) |
| `RESEARCH.md` | なぜそう判断できるか (リサーチ結果・根拠) | リサーチ | ユーザーリサーチ・インタビュー結果・発見事項を残すとき |
| `BRAND.md` | なぜこのトーンか | マーケ/クリエイティブ | ブランド・トーン・コピーライティング方針の議論 |
| `LEGAL.md` | なぜこの制約があるか | 横断 | 契約・ライセンス・プライバシーポリシー・コンプライアンスの議論 |
| `DEPLOYMENT.md` | いつどう出すか | 開発 | デプロイ・リリース・環境変数・インフラの議論 |
| `SEO.md` | どこで見つけてもらうか | マーケ | 検索最適化方針・サイト構造の議論 |
| `SECURITY.md` | どう守るか | 開発 | 認証・機密・脆弱性・コンプライアンスの議論 |
| `ANALYTICS.md` | どう測るか | マーケ | KPI・計測設計・イベント命名・ダッシュボードの議論 |
| `SALES.md` | どう売るか | 営業 | 提案書・価格・営業フローの議論 |

### 運用ルール

- **配置**: 大文字 + `.md` で **リポ root** に置く (matklad ARCHITECTURE.md convention)。肥大化したら `docs/` 配下への移動を検討
- **lazy 作成**: 該当 signal が出るまで作らない。空の placeholder は置かない
- **inline 更新**: 関連話題が出るたびその場で更新する (まとめて後でやらない)
- **自動更新 doc**: `ARCHITECTURE.md` と `CONTEXT.md` は grill-with-docs / improve-architecture が会話中に自動更新する
- **新 doc の追加**: Catalog にない doc が必要になったら、まずこの表に行を追加する

## AI agent 行動規範

開発規約 (GitHub Flow / ブランチ命名 / Conventional Commits / 破壊的操作禁止 / 推測 commit 禁止) は [CONTRIBUTING.md](./CONTRIBUTING.md) に従う。AI 特有の規律として以下を厳守:

- コミットメッセージ・PR 本文・Issue 本文に AI 生成の旨を記載しない (`🤖 Generated with...`、`Co-Authored-By: Claude...` 等は付与しない)

## 参照

- [CONTRIBUTING.md](./CONTRIBUTING.md) — 規約 (人間向け)
- [README.md](./README.md) — このリポジトリの概要と使い方
