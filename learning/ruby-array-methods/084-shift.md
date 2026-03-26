# Array#shift

- 日付: 2026-03-25
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-shift
- daily 記録: [daily/2026-03-25.md](../../daily/2026-03-25.md)

## 基本情報

```ruby
shift → obj or nil
shift(n) → new_array
```

`Array#shift` は、配列の先頭要素を削除し、その要素を返すメソッドです。このメソッドは**破壊的**で、元の配列を変更します。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# Removes and returns leading elements from self.
# 自身の先頭要素を削除し、それを返します。
# With no argument, removes and returns one element, if available, or nil otherwise:
# 引数がない場合は、要素があればそのうちの1つを取り出して返し、要素がない場合はnilを返します。
a = [0, 1, 2, 3]
a.shift  # => 0
a        # => [1, 2, 3]
[].shift # => nil

# With non-negative numeric argument count given, removes and returns the first count elements:
# 0以上の数値を引数countに指定すると、最初のcount個の要素を取り出し、その値を返します。
a = [0, 1, 2, 3]
a.shift(2)   # => [0, 1]
a            # => [2, 3]
a.shift(1.1) # => [2]
a            # => [3]
a.shift(0)   # => []
a            # => [3]

# If count is large, removes and returns all elements:
# count が大きい場合は、すべての要素を削除して返します。
a = [0, 1, 2, 3]
a.shift(50) # => [0, 1, 2, 3]
a           # => []

# If self is empty, returns a new empty array.
# Related: see Methods for Deleting.
```

## 深掘り・補足

### 重要なポイント

**破壊的メソッド**
- `shift` は元の配列を変更します
- 非破壊的な代替手段が必要な場合は、`first` と `drop` の組み合わせを検討してください

**返り値の違い**
- **引数なし**: 削除された単一の要素（または `nil`）を返す
- **引数あり**: 削除された要素の配列を返す（空配列の場合もあります）

**空配列での動作**
- 引数なし: `nil` を返す
- 引数あり: 新しい空配列 `[]` を返す（元の配列とは異なるオブジェクト）

### ユースケース

**キュー（FIFO）の実装**
```ruby
queue = [1, 2, 3, 4, 5]
while item = queue.shift
  puts "Processing: #{item}"
end
# Processing: 1
# Processing: 2
# ...
```

`shift` は、配列をキュー（先入れ先出し）として使う際に最適です。`push` と組み合わせることで、簡単にキュー構造を実装できます。

**パターンマッチングでの先頭要素の分離**
```ruby
arr = [:first, :second, :third]
head = arr.shift  # => :first
# arr は [:second, :third]
```

**バッチ処理**
```ruby
items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
batch = items.shift(3)  # => [1, 2, 3]
# items は [4, 5, 6, 7, 8, 9, 10]
```

### 注意点

**パフォーマンスへの影響**
- `shift` は配列の先頭要素を削除するため、残りの要素をすべて1つずつ前方にシフトする必要があります
- **時間計算量**: O(n)（配列のサイズに比例）
- 大量の要素を持つ配列で頻繁に `shift` を使う場合、パフォーマンスが低下する可能性があります

**代替手段**
- 頻繁に先頭からの削除が必要な場合は、`Queue` クラス（`require 'thread'`）や、より効率的なデータ構造の使用を検討してください

**浮動小数点数の引数**
```ruby
a = [1, 2, 3, 4]
a.shift(2.7)  # => [1, 2]（2.7 は整数に変換される）
```

引数に浮動小数点数を渡すと、整数に変換されます（小数点以下は切り捨て）。

### 関連メソッドとの比較

| メソッド | 動作 | 破壊的 | 返り値 |
|---------|------|--------|--------|
| `shift` | 先頭を削除 | Yes | 削除された要素 |
| `pop` | 末尾を削除 | Yes | 削除された要素 |
| `first` | 先頭を取得 | No | 先頭要素（配列は変更されない） |
| `drop(n)` | 先頭n個を除外 | No | 新しい配列（元の配列は変更されない） |
| `unshift` | 先頭に追加 | Yes | 変更後の配列 |
| `push` | 末尾に追加 | Yes | 変更後の配列 |

### まとめ

`shift` は、配列の先頭から要素を削除して取り出す際に便利なメソッドですが、パフォーマンスに注意が必要です。キューやバッチ処理などの用途に適していますが、大規模なデータや頻繁な操作が必要な場合は、より効率的なデータ構造の使用を検討してください。
