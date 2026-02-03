# Array#drop_while

- 日付: 2026-02-03
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-drop_while
- daily 記録: [daily/2026-02-03.md](../../daily/2026-02-03.md)

## 基本情報

```ruby
drop_while {|element| ... } → new_array
drop_while → new_enumerator
```

ブロックが与えられた場合、`self` の連続する各要素でブロックを呼び出します。ブロックが `false` または `nil` を返した場合、処理を停止します。ブロックが真の値を返した要素を省略した新しい配列を返します。`self` は変更しません。

ブロックが与えられなかった場合、新しい `Enumerator` を返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

drop と take が対になると聞いたので take_while もありそうだなと思ったら、あった

### コード例

```ruby
# drop_while {|element| ... } → new_array
# drop_while → new_enumerator

# With a block given, calls the block with each successive element of self; stops if the block returns false or nil; returns a new array omitting those elements for which the block returned a truthy value; does not modify self:
# ブロックが与えられた場合、`self` の連続する各要素でブロックを呼び出します。ブロックが `false` または `nil` を返した場合、処理を停止します。ブロックが真の値を返した要素を省略した新しい配列を返します。`self` は変更しません。
a = [0, 1, 2, 3, 4, 5]
a.drop_while {|element| element < 3 } # => [3, 4, 5]

# With no block given, returns a new Enumerator.
# ブロックが与えられなかった場合、新しい`Enumerator`を返します。
```

## 深掘り・補足

### 重要なポイント

1. **条件が偽になるまで要素を捨てる**: `drop_while` は配列の先頭から順に要素を評価し、ブロックが真を返す間は要素を捨て続けます。ブロックが偽（`false` または `nil`）を返した時点で処理を停止し、**その位置以降のすべての要素**を返します。

2. **非破壊的メソッド**: 元の配列 `self` は変更されず、新しい配列が返されます。

3. **一度偽になったら終了**: 重要なのは、ブロックが最初に偽を返した時点で処理が止まることです。その後の要素は評価されません。

### 関連メソッドとの比較

#### `drop` との違い

```ruby
a = [0, 1, 2, 3, 4, 5]

# drop: 指定した個数だけ先頭から捨てる
a.drop(3)  # => [3, 4, 5]

# drop_while: 条件を満たす間は捨てる
a.drop_while {|e| e < 3 }  # => [3, 4, 5]
```

- `drop`: 固定の個数を指定
- `drop_while`: 条件に基づいて動的に判断

#### `take_while` との対比

```ruby
a = [0, 1, 2, 3, 4, 5]

# take_while: 条件を満たす間は取得
a.take_while {|e| e < 3 }  # => [0, 1, 2]

# drop_while: 条件を満たす間は捨てる
a.drop_while {|e| e < 3 }  # => [3, 4, 5]
```

- `take_while`: 条件を満たす要素を取得
- `drop_while`: 条件を満たす要素を捨てる（それ以降を取得）

### ユースケース

1. **ヘッダー行のスキップ**

```ruby
lines = ["# Header", "## Subheader", "Content line 1", "Content line 2"]
content = lines.drop_while {|line| line.start_with?("#") }
# => ["Content line 1", "Content line 2"]
```

2. **閾値を超えるまでの要素をスキップ**

```ruby
numbers = [1, 5, 8, 15, 20, 10, 5]
above_threshold = numbers.drop_while {|n| n < 10 }
# => [15, 20, 10, 5]  # 10未満をスキップし、10以上が出現した時点で残りすべて取得
```

3. **条件を満たさなくなった地点以降を処理**

```ruby
temperatures = [15, 18, 22, 25, 28, 26, 23]
hot_onwards = temperatures.drop_while {|t| t < 25 }
# => [25, 28, 26, 23]  # 25度以上になった時点から最後まで
```

### 注意点

1. **一度条件が偽になったら、その後は評価しない**

```ruby
a = [1, 3, 5, 2, 7, 9]
a.drop_while {|e| e.odd? }  # => [2, 7, 9]
# 2（偶数）が出現した時点で終了。その後の 7, 9 も含まれる
```

2. **空配列が返る可能性**

```ruby
a = [1, 2, 3]
a.drop_while {|e| e < 10 }  # => []
# すべての要素が条件を満たす場合、空配列が返る
```

3. **ブロックなしの場合は Enumerator**

```ruby
a = [0, 1, 2, 3, 4, 5]
enum = a.drop_while  # => #<Enumerator: ...>
enum.each {|e| e < 3 }  # => [3, 4, 5]
```

### まとめ

`drop_while` は、配列の先頭から条件を満たす要素を捨て続け、条件を満たさなくなった時点以降のすべての要素を返すメソッドです。`take_while` との対として覚えると理解しやすく、データのフィルタリングや前処理で非常に有用です。
