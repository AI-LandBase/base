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

## AI agent 行動規範

- コミットメッセージ・PR 本文に AI 生成の旨を記載しない (`🤖 Generated with...`、`Co-Authored-By: Claude...` 等は付与しない)
- main ブランチに直接 commit / push しない
- `git push --force`、`git reset --hard`、`git branch -D`、`git clean -fd` 等の破壊的操作は明示許可なく実行しない
- 「動くはず」「テスト通るはず」での commit / PR は禁止。実行確認した上で commit する

## 参照

- [CONTEXT.md](./CONTEXT.md) — 用語集
- [CONTRIBUTING.md](./CONTRIBUTING.md) — 規約 (人間向け)
- [README.md](./README.md) — Base とは何か、使い方
