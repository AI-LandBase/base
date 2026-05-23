---
name: finishing-branch
description: Use when implementation is complete, all tests pass, and you need to decide how to integrate the work - guides completion of development work by presenting structured options for merge, PR, or cleanup. Also activate when user says 「PR 出して」「マージ準備」「ブランチ閉じて」「実装終わったので統合」「squash merge」.
---

# finishing-branch

実装完了後の統合フェーズを構造化する。test verify → 状態確認 → 選択肢提示 → 実行 → cleanup。

**Core principle:** Verify tests → Detect PR state → Present options → Execute → Clean up.

## Step 1: Verify Tests

オプション提示前に必ずテストを通す。

```bash
# プロジェクトのテストスイートを実行 (該当するもの)
npm test / pnpm test / pytest / cargo test / go test ./... / bundle exec rspec
```

**失敗していたら停止:**

```
Tests failing (<N> failures). 修正してから再開してください。

[失敗内容]
```

**通ったら Step 2 へ。**

## Step 2: Detect Base Branch and PR State

```bash
# Base branch を判定
BASE=$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@' || echo main)

# 現在のブランチ
CURRENT=$(git branch --show-current)

# このブランチに対する開いている PR があるか
gh pr view --json state,number,reviewDecision 2>/dev/null
```

PR 状態に応じて Step 3 のメニューでデフォルト推奨が変わる:
- **PR 無し** → Option 1 (PR作成) を推奨
- **PR 存在 + APPROVED** → Option 2 (squash merge) を推奨
- **PR 存在 + 未承認/CHANGES_REQUESTED** → ユーザーにフィードバック対応を促す

## Step 3: Present Options

```
実装完了。何をしますか?

1. Push して Pull Request を作成   (PR 未作成のとき)
2. 承認済み PR をマージして branch 削除  (PR が approve されたとき)
3. このブランチを保持して後で対応
4. この作業を破棄

選んでください (1-4):
```

## Step 4: Execute Choice

### Option 1: Push and Create PR

```bash
# branch を push
git push -u origin "$CURRENT"

# PR テンプレート (.github/PULL_REQUEST_TEMPLATE.md) を読み込んで埋める
TEMPLATE=$(cat .github/PULL_REQUEST_TEMPLATE.md 2>/dev/null)
```

PR 本文は **`.github/PULL_REQUEST_TEMPLATE.md` の構造を保ったまま** 各セクションを埋める:

- 概要
- 変更内容
- テスト方法 (実行コマンド)
- チェックリスト (Conventional Commits 準拠、AI footer 無し、等)
- Closes #<Issue 番号>  ← branch 名から抽出 (`feature/42-...` → `Closes #42`)

**PR title は squash merge 時にそのまま main の commit subject になる** ため、`(issue#<N>)` を必ず含める (CONTRIBUTING.md のコミット規約と一致させる):

```bash
gh pr create \
  --title "<type>(<scope>): <subject> (issue#<N>)" \
  --body "$(cat <<'EOF'
## 概要

<1-2文>

## 変更内容

- <主要な変更点1>
- <主要な変更点2>

## テスト方法

\`\`\`bash
<実行コマンド>
\`\`\`

## チェックリスト

- [x] 受け入れ基準 (Must Have) を満たす
- [x] テスト追加・更新
- [x] ドキュメント更新 (必要なら)
- [x] Conventional Commits 準拠
- [x] AI 生成メッセージを含まない

Closes #<N>
EOF
)"
```

完了後、PR URL をユーザーに渡す。

### Option 2: Merge Approved PR (squash + delete branch)

事前に PR が approve されているか確認:

```bash
gh pr view --json state,reviewDecision,mergeable
```

`state=OPEN`、`reviewDecision=APPROVED`、`mergeable=MERGEABLE` でなければ:

```
PR がまだマージ可能な状態ではありません:
- review: <reviewDecision>
- mergeable: <mergeable>

レビューフィードバック対応 / コンフリクト解消が必要です。
```

条件を満たしていれば実行:

```bash
gh pr merge --squash --delete-branch
```

完了後、ローカルもクリーンアップ:

```bash
git checkout "$BASE"
git pull origin "$BASE"
git branch -d "$CURRENT" 2>/dev/null || true  # 既にdeleted-branch扱いなら無視
```

### Option 3: Keep As-Is

```
ブランチ <name> を保持します。後で finishing-branch を再実行すると再開できます。
```

何もしない。

### Option 4: Discard

**型一致の確認を取る:**

```
以下を **完全に削除** します:
- ブランチ <name>
- 全コミット: <commit-list>

`discard` と入力して確定してください:
```

ユーザーが exact "discard" を入力したら:

```bash
git checkout "$BASE"
git branch -D "$CURRENT"

# 既に push 済みなら remote の branch も削除
git push origin --delete "$CURRENT" 2>/dev/null || true
```

それ以外の入力なら中止。

## Quick Reference

| Option | Push | PR | Merge | Branch delete |
|--------|------|----|----|--------------|
| 1. PR作成 | yes | create | - | - |
| 2. PR マージ | - | merge | squash | yes (remote + local) |
| 3. 保持 | - | - | - | - |
| 4. 破棄 | - | - | - | yes (force, remote + local) |

## Common Mistakes

**テスト未確認で進める**
- 問題: 壊れたコードを PR にする / 壊れた状態でマージ
- 対処: Step 1 で必ず通す。失敗なら停止

**承認前にマージ**
- 問題: レビュー無しでmainに混入
- 対処: Option 2 では `reviewDecision=APPROVED` を確認

**`--squash` 忘れ**
- 問題: PR のコミットが全部main履歴に残り「1 PR = 1 commit」原則崩壊
- 対処: 必ず `gh pr merge --squash --delete-branch`

**`--delete-branch` 忘れ**
- 問題: マージ済 branch が remote / local に残る
- 対処: `--delete-branch` フラグと、local の `git branch -d` を併用

**破棄前の確認を省く**
- 問題: 誤って作業を消す
- 対処: Option 4 では `discard` の type 確認を必須にする

## Red Flags

**Never:**
- 失敗テストのまま進める
- 承認なしでマージ
- `--squash` 無しで merge
- 確認なしで破棄
- `git push --force` を勝手に実行

**Always:**
- Step 1 でテスト通過確認
- Step 2 で PR 状態確認
- Option 2 では squash + delete-branch
- Option 4 では type 確認
