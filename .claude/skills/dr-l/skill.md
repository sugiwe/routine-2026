---
name: dr-l
description: 学習ノート（learning note）を作成
---

# Daily Routine Learning Note (dr-l)

学習内容の詳細ノートを作成します。Sociomedia HIG または Ruby Array Methods の学習記録を作成できます。

## 使い方

```
# Sociomedia HIG の学習ノート作成
/dr-l shig "モバイルでは包括的より階層的に" https://www.sociomedia.co.jp/9801 "メモ内容"

# Ruby Array Methods の学習ノート作成
/dr-l ruby "select" https://docs.ruby-lang.org/en/4.0/Array.html#method-i-select "メモ内容"

# メモは省略可能
/dr-l shig "一貫性の原則" https://www.sociomedia.co.jp/xxxx
/dr-l ruby "append" https://docs.ruby-lang.org/en/4.0/Array.html#method-i-append
```

## 引数の形式

```
/dr-l <種別> <トピック名/メソッド名> <URL> [メモ]
```

- **種別**: `shig` または `ruby`
- **トピック名/メソッド名**: 学習した内容の名前
- **URL**: ソースページのURL
- **メモ**: （省略可）ユーザーの第一印象や補足

## 実行内容

1. 今日の日次ファイルを更新（学習セクションにチェック済み・URLを記録）
2. 既存の学習ノートの連番を確認
3. WebFetch でソースページを取得
4. 詳細ノートを作成（`learning/` 以下）
5. 日次ファイルに詳細ノートへのリンクを追加
6. `_sidebar.md` を更新
7. 完了メッセージを表示

## 処理の流れ

### 1. 日次ファイルの更新

今日の `daily/YYYY-MM-DD.md` を開き、該当セクションを更新：

**Sociomedia HIG の場合**:
```markdown
### Sociomedia ヒューマンインターフェース ガイドライン

- [x] 今日のトピック: 82. モバイルでは包括的より階層的に
- URL: https://www.sociomedia.co.jp/9801
- メモ: （引数のメモ）
- 詳細ノート: （後で追加）
```

**Ruby の場合**:
```markdown
### Ruby 4.0 Documentation - Array Instance Methods

- [x] 今日のメソッド: `select`
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-select
- メモ: （引数のメモ）
- 詳細ノート: （後で追加）
```

### 2. 連番の確認

既存ファイルを確認して次の連番を決定：

```bash
# Sociomedia HIG
ls -1 learning/sociomedia-hig/ | tail -1
# 例: 081-left-goes-back-right-goes-forward.md
# → 次は 082

# Ruby Array Methods
ls -1 learning/ruby-array-methods/ | tail -1
# 例: 081-sample.md
# → 次は 082
```

### 3. WebFetch でソース取得

```markdown
WebFetch を使ってソースページを取得し、内容を分析：
- Sociomedia HIG: 概念の説明、実践的なポイント、良い例・悪い例
- Ruby: メソッドの詳細、使い方、関連メソッド、パフォーマンス
```

### 4. 詳細ノートの作成

#### Sociomedia HIG の場合

ファイル名: `learning/sociomedia-hig/{連番}-{トピック名スラッグ}.md`

```markdown
# {トピック名}

- 日付: 2026-03-23
- URL: {URL}
- daily 記録: [daily/2026-03-23.md](../../daily/2026-03-23.md)

## ユーザーの第一印象（daily より）

{引数のメモ}

## 深掘り・補足

{WebFetch で取得した内容を元に、以下を追加}
- 概念の説明
- 重要なポイント
- 良い例と悪い例
- 関連する原則やガイドライン
```

#### Ruby の場合

ファイル名: `learning/ruby-array-methods/{連番}-{メソッド名スラッグ}.md`

```markdown
# Array#{メソッド名}

- 日付: 2026-03-23
- URL: {URL}
- daily 記録: [daily/2026-03-23.md](../../daily/2026-03-23.md)

## 基本情報

{メソッドシグネチャと公式ドキュメントの概要}

## ユーザーの学習記録（daily より）

### 第一印象のメモ

{引数のメモ}

### コード例

{daily に記録されたコード例}

## 深掘り・補足

{WebFetch で取得した内容を元に、以下を追加}
- 重要なポイント
- ユースケース
- 注意点
- 関連メソッドとの比較
- パフォーマンスの考慮事項
```

### 5. 日次ファイルにリンク追加

詳細ノートへの相対パスを追加：

```markdown
- 詳細ノート: [learning/sociomedia-hig/082-hierarchical-rather-than-comprehensive-for-mobile.md](../learning/sociomedia-hig/082-hierarchical-rather-than-comprehensive-for-mobile.md)
```

### 6. _sidebar.md の更新

新しいノートを一番上に追加（降順）：

**Sociomedia HIG**:
```markdown
* **学習ノート**
  * **Sociomedia HIG**
    * [082. モバイルでは包括的より階層的に](learning/sociomedia-hig/082-hierarchical-rather-than-comprehensive-for-mobile.md)
    * [081. 左が戻るで右が進む](learning/sociomedia-hig/081-left-goes-back-right-goes-forward.md)
    ...
```

**Ruby Array Methods**:
```markdown
  * **Ruby Array Methods**
    * [082. select](learning/ruby-array-methods/082-select.md)
    * [081. sample](learning/ruby-array-methods/081-sample.md)
    ...
```

### 7. 完了メッセージ

```markdown
✅ 学習ノートを作成しました！

📚 Sociomedia HIG 082: モバイルでは包括的より階層的に
- 詳細ノート: learning/sociomedia-hig/082-hierarchical-rather-than-comprehensive-for-mobile.md
- 日次記録を更新
- _sidebar.md を更新

詳細ノートには以下を追加しました：
- 概念の説明と実践的なポイント
- 良い例と悪い例
- 関連する原則やガイドライン
```

## 注意事項

- **ファイル名のスラッグ化**: トピック名を英語の小文字、ハイフン区切りに変換
- **記号のメソッド名**: Ruby の `&`, `*`, `<<` などは単語化（`ampersand`, `asterisk`, `left-shift`）
- **自動翻訳の修正**: 明らかな誤訳があれば修正する

## 実装のポイント

1. **引数のパース**: スペースで分割し、種別・トピック名・URL・メモを抽出
2. **WebFetch の活用**: ソースページから有益な情報を抽出
3. **深掘りの質**: ただの要約ではなく、実践的な補足を追加
4. **日本語対応**: 自然な日本語で記述

---

## 実装

それでは、上記の仕様に従って、学習ノートを作成してください。

引数: $ARGUMENTS
