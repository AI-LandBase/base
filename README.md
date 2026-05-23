# Base

新規プロジェクトの上流に置く、用途非依存のスターター足場。AI / 人間の協働インフラだけを提供する。

## これは何

- 案件種別 (Web、CLI、マーケティング、調査等) を問わず、新規プロジェクト着手時に最初に使う土台
- アプリコード・フレームワーク・依存物は持たない
- AI agent (Claude Code) 用 workflow (`.claude/skills/`)、人間向け規約 (`CONTRIBUTING.md`)、GitHub テンプレ (`.github/`) のみで構成

## 使い方

GitHub の「Use this template」または gh CLI:

```bash
gh repo create my-new-project --template <owner>/base --private --clone
cd my-new-project
```

## 入っているもの

| パス | 役割 |
|------|------|
| `.claude/skills/` | Issue 作成 / TDD / 診断 / リファクタ / コミット / PR の workflow |
| `.github/ISSUE_TEMPLATE/` | 工数見積込みの Issue テンプレ |
| `.github/PULL_REQUEST_TEMPLATE.md` | PR テンプレ (squash 前提) |
| `CLAUDE.md` | AI agent 向け指針 |
| `CONTRIBUTING.md` | 規約・禁止事項 (人間向け) |
| `CONTEXT.md` | 用語集 |

## 入っていないもの

- アプリコード・フレームワーク・依存物 (`package.json`、`Gemfile` 等)
- Docker / CI / デプロイ設定
- 言語固有の linter / formatter 設定

これらは各プロジェクトで個別に追加する。

## ドキュメント

- [CLAUDE.md](./CLAUDE.md)
- [CONTRIBUTING.md](./CONTRIBUTING.md)
- [CONTEXT.md](./CONTEXT.md)

## ライセンス

MIT — [LICENSE](./LICENSE) 参照
