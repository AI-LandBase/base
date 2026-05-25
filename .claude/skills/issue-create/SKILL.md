---
name: issue-create
description: Create a new GitHub Issue using the project's feature template (concept, FR/NFR, technical spec, acceptance criteria, 工数見積, Definition of Done). Walks the user through each section interactively, builds the full markdown body, then calls `gh issue create`. Use when user wants to file an issue. Also activate when user says 「Issue 作って」「Issue 化」「issue を立てる」「チケット切って」「課題化」.
---

# issue-create

`.github/ISSUE_TEMPLATE/feature.md` をベースに、対話で Issue 本文を組み立て `gh issue create` で発行する。

## Pre-flight

1. **リポが GitHub remote を持つか**

   ```bash
   gh repo view --json nameWithOwner
   ```

   無ければユーザーに GitHub リポ作成を促す。

2. **テンプレートの存在確認**

   ```bash
   test -f .github/ISSUE_TEMPLATE/feature.md
   ```

   無ければユーザーに「Base 由来のリポではないようですが、テンプレ無しで進めますか?」と確認。

## Workflow

### 1. テンプレ読み込み

`.github/ISSUE_TEMPLATE/feature.md` を読み、各セクションの構造を把握する。

### 2. 概要を聞く

「何の Issue ですか? 1-2 文で。」

短い説明から、続くセクションを埋めるための context を作る。

### 3. Draft を一気に組み立てる

概要から AI が全セクションを埋めた draft 全文を **一度に** 提示する。セクション単位の往復はしない。

埋めるべき項目の参照リスト:

| セクション | 内容 |
|----------|------|
| 背景・課題 | 現状の問題、なぜ必要か |
| 目的・ゴール | 主目的、副次目標 |
| 要件定義 (FR) | 機能要件 (箇条書き) |
| 要件定義 (NFR) | パフォーマンス、セキュリティ、保守性 (該当するもののみ) |
| 技術仕様 | アーキ、実装方針 (この段階で確定できる範囲で) |
| 受け入れ基準 | Must Have / Should Have / Nice to Have |
| 工数見積 | タスク分解 (設計/実装/テスト/ドキュメント) と時間、確実性レベル |
| テスト計画 | 主要テストケース |
| Definition of Done | 完了条件 |
| 関連資料 | 参考リンクなど (任意) |
| 優先度・難易度・依存 | High/Medium/Low、依存 Issue |

**判断材料が足りないセクションがある場合は、issue-create を中断して grill-with-docs を提案する。** issue-create 内で長いヒアリングはしない (それは grill の責務)。grill で要件が固まってから issue-create に戻る。

### 4. 工数見積のガイド

| 確実性 | バッファ | 例 |
|-------|---------|-----|
| 高 | +10% | 過去に類似実装あり、よく知っている技術 |
| 中 | +25% | 一般的なパターン、ある程度経験あり |
| 低 | +50% | 初めての技術、複雑な仕様 |

タスク分解の標準割合: 設計 15-20%、実装 40-50%、テスト 20-25%、ドキュメント 10-15%。

合計が 16 時間 (2 営業日) を超える場合、Issue 分割を提案する。

### 5. タイトルと本文の組み立て

- **タイトル**: 概要から簡潔に。50文字以内推奨。type プレフィックスは付けない (label で表現)
- **本文**: テンプレ構造を保ったまま、ヒアリング内容を埋め込む
- **labels** (任意): 優先度・難易度・type を表現したい場合

### 6. ユーザー最終確認

組み立てた本文全体をユーザーに見せて、修正を反映。

### 7. 発行

```bash
gh issue create \
  --title "<タイトル>" \
  --body "$(cat <<'EOF'
<本文>
EOF
)"
```

label を付ける場合:

```bash
gh issue create --title "..." --body "..." --label "priority:high,difficulty:medium"
```

(label がリポに存在しない場合はエラーになる。事前確認するか、ラベル付けは後でユーザーに任せる)

### 8. URL を出力

`gh issue create` の戻り値 (Issue URL) をそのままユーザーに渡す。

## 禁止事項

- 本文に AI 生成の旨を記載しない (`🤖 Generated with...` 等)
- ユーザーに確認せずに `--label` を勝手に付けない (ラベル体系はプロジェクト依存)
- 工数見積を勝手に決めない。ユーザーが見積れない場合は「過去の類似Issueを参照」「タスク分解を細かくして見積る」を提案

## エッジケース

- **テンプレが無い**: 軽量フォーマット (概要 / 受け入れ基準 / 工数見積 だけ) でユーザーに提案
- **既存 Issue と類似**: `gh issue list --search "..."` で重複チェックを提案
- **複数の関心事が混ざっている**: 「これは2つの Issue に分けた方が良いです」と提案
