---
name: dr-f
description: 今日の習慣記録を完了（コミット・プッシュ・PR作成）
---

# Daily Routine Finish (dr-f)

今日の習慣記録を完了し、Git コミット、プッシュ、PR 作成を実行します。

## 使い方

```
/dr-f
```

引数は不要です。現在のブランチと変更内容を自動的に検出し、適切なコミットメッセージと PR を作成します。

## 実行内容

1. 現在のブランチ名を確認（例: `2026-03-23`）
2. 変更内容を確認（`git status`, `git diff`）
3. ステージング（`git add`）
4. コミット（日本語のコミットメッセージ）
5. プッシュ（`git push -u origin`）
6. PR 作成（日本語のタイトルと本文）
7. **セッション記録の保存** ← 🆕 NEW!
8. PR URL を表示

## 処理の流れ

### 1. 現在の状態を確認

```bash
# ブランチ名を取得
git branch --show-current
# 例: 2026-03-23

# 変更内容を確認
git status
git diff
```

### 2. 変更内容の分析

以下のファイルが変更されているか確認：

- `daily/YYYY-MM-DD.md`: 日次記録
- `learning/sociomedia-hig/XXX-*.md`: Sociomedia HIG の学習ノート
- `learning/ruby-array-methods/XXX-*.md`: Ruby の学習ノート
- `_sidebar.md`: サイドバーの更新

### 3. ステージングとコミット

```bash
git add daily/2026-03-23.md
git add learning/sociomedia-hig/082-*.md
git add learning/ruby-array-methods/082-*.md
git add _sidebar.md

git commit -m "$(cat <<'EOF'
2026-03-23 の日次記録を追加

## 完了した習慣
- ラジオ体操
- シダキュア
- Typnix 練習
- スクワット 20 回
- 腕立て伏せ 20 回
- 腹筋 20 回

## 学習内容
- Sociomedia HIG: 082. モバイルでは包括的より階層的に
- Ruby: Array#select

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"
```

### 4. コミットメッセージの生成

日次ファイルを読み込み、以下の内容を自動生成：

```markdown
YYYY-MM-DD の日次記録を追加

## 完了した習慣
{チェック済みの習慣を列挙}

## 学習内容
{学習したトピック/メソッドを列挙}

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

### 5. プッシュ

```bash
git push -u origin 2026-03-23
```

### 6. PR 作成

```bash
gh pr create --title "2026-03-23 の日次記録" --body "$(cat <<'EOF'
## 概要
2026-03-23 の習慣記録と学習ノートを追加

## 完了した習慣
- ✅ ラジオ体操
- ✅ シダキュア
- ✅ Typnix 練習
- ✅ スクワット 20 回
- ✅ 腕立て伏せ 20 回
- ✅ 腹筋 20 回

## 学習内容

### Sociomedia HIG 082
**モバイルでは包括的より階層的に**
- URL: https://www.sociomedia.co.jp/9801
- モバイルとデスクトップの UI 設計アプローチの違い
- 階層的設計のメリット・デメリットと実践的なポイント

### Ruby Array Methods 082
**Array#select**
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-select
- 配列から条件を満たす要素を抽出する非破壊的メソッド
- filter との関係、関連メソッドとの比較

## 変更内容
- 日次記録: `daily/2026-03-23.md`
- 学習ノート: `learning/sociomedia-hig/082-*.md`
- 学習ノート: `learning/ruby-array-methods/082-*.md`
- サイドバー更新: `_sidebar.md`

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

### 7. PR 本文の生成

日次ファイルと学習ノートを読み込み、以下の内容を自動生成：

```markdown
## 概要
{日付} の習慣記録と学習ノートを追加

## 完了した習慣
{チェック済みの習慣を ✅ マーク付きで列挙}

## 学習内容

### Sociomedia HIG {連番}
**{トピック名}**
- URL: {URL}
- {簡潔な説明}

### Ruby Array Methods {連番}
**Array#{メソッド名}**
- URL: {URL}
- {簡潔な説明}

## 変更内容
- 日次記録: `daily/YYYY-MM-DD.md`
- 学習ノート: （該当するファイル）
- サイドバー更新: `_sidebar.md`

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

### 8. セッション記録の保存 🆕

PR 作成が完了したら、今日のセッションの記録を `memory/sessions/YYYY-MM-DD-session.md` に保存します。

#### セッション記録のフォーマット

```markdown
# YYYY-MM-DD セッション記録

## 達成した習慣

### 基本習慣
- ✅ ラジオ体操
- ✅ シダキュア
- ✅ Typnix 練習

### エクササイズ
- ✅ スクワット 20 回完了
- ✅ 腕立て伏せ 20 回完了
- ✅ 腹筋 20 回完了
- ❌ エクササイズ動画（未達成）

## 今日の気づき・メモ

### Sociomedia HIG {連番}
**{トピック名}**
- {ユーザーのメモ}
- {学習内容の簡潔な要約}

### Ruby Array Methods {連番}
**{メソッド名}**
- {学習内容の簡潔な要約}

## 学習の進捗
- Sociomedia HIG: {連番}/100 ({パーセンテージ}%)
- Ruby Array Methods: {連番} (進行中)

## セッションの特徴
- {セッション中に使用したスキルや特徴的な出来事}

## 次回へのメモ
- {未達成の習慣や次回やりたいこと}
- {その他、翌日に気をつけたいこと}
```

#### セッション記録の目的

- **記憶の引き継ぎ**: 次回のセッション（`dr-s`）で読み込んで、前回の状況を思い出す
- **習慣の継続性の把握**: 達成・未達成の習慣を記録
- **学習の進捗管理**: どこまで学習が進んだかを追跡
- **気づきの保存**: 日次ファイルのメモだけでなく、セッション全体の振り返りを保存

### 9. 完了メッセージ

```markdown
✅ すべて完了しました！

## コミット
- 2026-03-23 の日次記録を追加

## プッシュ
- ブランチ `2026-03-23` をリモートにプッシュ完了

## PR 作成
**PR URL**: https://github.com/sugiwe/routine-2026/pull/XX

## セッション記録
- `memory/sessions/2026-03-23-session.md` に今日のセッションを保存

今日もお疲れさまでした！
```

## 注意事項

- **変更がない場合**: エラーメッセージを表示し、処理を中断
- **コミット失敗**: pre-commit フックなどでエラーが出た場合、ユーザーに報告
- **PR 作成失敗**: エラー内容を表示し、手動での対応を促す
- **すべて日本語**: コミットメッセージ、PR タイトル、PR 本文は日本語で記述

## 実装のポイント

1. **日次ファイルの解析**: チェック済み項目を抽出
2. **学習ノートの解析**: トピック名、URL、概要を抽出
3. **適切なエラーハンドリング**: 各ステップで失敗時の対応
4. **自然な日本語**: 機械的でない、読みやすい文章

---

## 実装

それでは、上記の仕様に従って、今日の習慣記録を完了してください。

引数: $ARGUMENTS
