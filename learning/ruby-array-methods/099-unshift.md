# Array#unshift

- 日付: 2026-04-09
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-unshift
- daily 記録: [daily/2026-04-09.md](../../daily/2026-04-09.md)

## 基本情報

**メソッドシグネチャ**:
```ruby
unshift(*objects) → self
```

**概要**:
配列の**先頭に要素を追加**する破壊的メソッドです。複数の要素を一度に追加でき、修正後の配列全体を返します。

**エイリアス**: `prepend`

## ユーザーの学習記録（daily より）

### 第一印象のメモ

pushは末尾に追加だったかな？

### コード例

```ruby
# Prepends the given objects to self:
# 指定されたオブジェクトを自身の先頭に追加します。
a = [:foo, 'bar', 2]
a.unshift(:bam, :bat) # => [:bam, :bat, :foo, "bar", 2]

# Related: Array#shift; see also Methods for Assigning.
# Also aliased as: prepend
```

## 深掘り・補足

### 基本的な動作

`unshift` は「Prepends leading elements」（先頭に要素を追加する）メソッドで、元の配列を直接変更する**破壊的メソッド**です。

```ruby
arr = [2, 3, 4, 5]
arr.unshift(0, 1)
# => [0, 1, 2, 3, 4, 5]

# 元の配列が変更される
arr
# => [0, 1, 2, 3, 4, 5]
```

### 返り値

修正後の配列全体を返します。これにより、メソッドチェーンが可能になります。

```ruby
arr = [2, 3]
arr.unshift(1).unshift(0)
# => [0, 1, 2, 3]
```

### 関連メソッドとの対応関係

| メソッド | 動作 | 位置 | 破壊的 |
|---------|------|------|--------|
| `unshift` / `prepend` | 要素を追加 | 先頭 | ✅ |
| `shift` | 要素を削除・取得 | 先頭 | ✅ |
| `push` / `append` | 要素を追加 | 末尾 | ✅ |
| `pop` | 要素を削除・取得 | 末尾 | ✅ |

#### 覚え方

- **先頭**: `unshift`（追加）⇔ `shift`（削除）
- **末尾**: `push`（追加）⇔ `pop`（削除）

### 実践的な使い方

#### 複数要素の一括追加

```ruby
arr = [3, 4, 5]
arr.unshift(1, 2)
# => [1, 2, 3, 4, 5]
```

#### 可読性の高いエイリアス

`prepend` を使うとより意図が明確になります：

```ruby
headers = ['Name', 'Age']
headers.prepend('ID')
# => ['ID', 'Name', 'Age']
```

#### スタック・キューの実装

```ruby
# スタック (LIFO: Last In First Out)
stack = []
stack.unshift(1)  # 先頭に追加
stack.unshift(2)
stack.shift       # => 2 (先頭から取り出し)
stack.shift       # => 1

# キュー (FIFO: First In First Out)
queue = []
queue.push(1)     # 末尾に追加
queue.push(2)
queue.shift       # => 1 (先頭から取り出し)
queue.shift       # => 2
```

### パフォーマンスの考慮事項

⚠️ **大規模な配列では非効率**になる可能性があります。

先頭に追加するたびに全要素の再インデックスが必要なため、内部的には以下のような処理が発生します：

```
[1, 2, 3]
     ↓ unshift(0) を実行
[0, 1, 2, 3]
 ↑  ↑  ↑  ↑
 新 すべての要素をシフト
```

頻繁な先頭追加が必要な場合は、以下の代替手段を検討してください：

- 逆順で `push` を使う → 最後に `reverse` する
- リンクリストなどの異なるデータ構造を使う
- `Array#insert(0, element)` も同様のコスト

### push との違い（まとめ）

ユーザーのメモにあった通り、`push` は末尾に追加、`unshift` は先頭に追加です：

```ruby
arr = [2, 3]
arr.push(4)       # => [2, 3, 4]（末尾に追加）
arr.unshift(1)    # => [1, 2, 3, 4]（先頭に追加）
```

### まとめ

- **目的**: 配列の先頭に要素を追加
- **破壊的**: 元の配列を変更
- **返り値**: 変更後の配列全体
- **エイリアス**: `prepend`（より可読性が高い）
- **注意点**: 大規模配列では非効率（全要素のシフトが必要）
- **対応メソッド**: `shift`（先頭から削除）、`push`（末尾に追加）、`pop`（末尾から削除）
