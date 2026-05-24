# Base

新規プロジェクトの上流に置く、用途非依存のスターター足場。AI / 人間の協働インフラだけを提供する。

## これは何

- 案件種別 (Web、CLI、マーケティング、調査等) を問わず、新規プロジェクト着手時に最初に使う土台
- アプリコード・フレームワーク・依存物は持たない
- AI agent (Claude Code) 用 workflow (`.claude/skills/`)、人間向け規約 (`CONTRIBUTING.md`)、GitHub テンプレ (`.github/`) のみで構成

## 使い方

[Use this template](https://github.com/zomians/base/generate) または gh CLI:

```bash
gh repo create my-new-project --template zomians/base --private --clone
cd my-new-project
```

### 最初の一歩

1. `CLAUDE.md` に目を通す (AI agent への指針 / skill 索引)
2. 最初のアイデアがあれば「設計を詰めたい」と Claude に話しかける
   → `grill-with-docs` が起動し、CONTEXT.md / ADR を作りながら設計が固まる
3. 固まったら「Issue 作って」で `issue-create` が起動 (工数見積込みの Issue が立つ)

## 入っているもの

| パス | 役割 |
|------|------|
| `.claude/skills/` | Issue 作成 / TDD / 診断 / リファクタ / コミット / PR の workflow |
| `.github/ISSUE_TEMPLATE/` | 工数見積込みの Issue テンプレ |
| `.github/PULL_REQUEST_TEMPLATE.md` | PR テンプレ (squash 前提) |
| `CLAUDE.md` | AI agent 向け指針 |
| `CONTRIBUTING.md` | 規約・禁止事項 (人間向け) |

## 入っていないもの

- アプリコード・フレームワーク・依存物 (`package.json`、`Gemfile` 等)
- Docker / CI / デプロイ設定
- 言語固有の linter / formatter 設定

これらは各プロジェクトで個別に追加する。

## ドキュメント

- [CLAUDE.md](./CLAUDE.md) — AI agent 向け指針 (規律 / skill 索引 / フロー / ドキュメント構造)
- [CONTRIBUTING.md](./CONTRIBUTING.md) — 規約・禁止事項

## ライセンス

MIT — [LICENSE](./LICENSE) 参照
