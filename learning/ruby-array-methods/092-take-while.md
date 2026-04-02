# Array#take_while

- 日付: 2026-04-02
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-take_while
- daily 記録: [daily/2026-04-02.md](../../daily/2026-04-02.md)

## 基本情報

```ruby
take_while {|element| ... } → new_array
take_while → new_enumerator
```

ブロックが与えられた場合、配列の各要素に対して順番にブロックを呼び出します。ブロックが `false` または `nil` を返すと繰り返しを停止し、ブロックが真（truthy）の値を返した要素のみを含む新しい配列を返します。

**重要**: 自身（元の配列）を変更しません。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# With a block given, calls the block with each successive element of self; stops iterating if the block returns false or nil; returns a new array containing those elements for which the block returned a truthy value:
# ブロックが与えられた場合、selfの各要素に対して順番にブロックを呼び出します。ブロックがfalseまたはnilを返すと繰り返しを停止します。ブロックが真（truthy）の値を返した要素のみを含む新しい配列を返します。
a = [0, 1, 2, 3, 4, 5]
a.take_while {|element| element < 3 } # => [0, 1, 2]
a.take_while {|element| true }        # => [0, 1, 2, 3, 4, 5]
a.take_while {|element| false }       # => []

# With no block given, returns a new Enumerator.
# Does not modify self.
# 自身を変更しません。
# Related: see Methods for Fetching.
```

## 深掘り・補足

### メソッドの動作

`take_while` は配列の**先頭から順に**要素を取り出し、条件が満たされている間だけ要素を収集します。条件が満たされなくなった時点で、**即座に処理を停止**します。

**重要なポイント**:
- 先頭から順番に評価
- 最初に条件が `false` になった時点で停止
- それ以降の要素は評価されない（効率的）

### 関連メソッドとの比較

#### `take` との違い

```ruby
# take: 個数を指定して先頭から取得
a = [0, 1, 2, 3, 4, 5]
a.take(3)  # => [0, 1, 2]

# take_while: 条件で取得
a.take_while {|x| x < 3 }  # => [0, 1, 2]
```

- **`take`**: 固定の個数を取得
- **`take_while`**: 条件に基づいて取得（個数は可変）

#### `drop_while` との関係

`drop_while` は逆の動作をします：

```ruby
a = [0, 1, 2, 3, 4, 5]

# take_while: 条件を満たす間は「取る」
a.take_while {|x| x < 3 }  # => [0, 1, 2]

# drop_while: 条件を満たす間は「捨てる」
a.drop_while {|x| x < 3 }  # => [3, 4, 5]
```

#### `select` との違い

```ruby
a = [0, 1, 2, 3, 4, 5]

# take_while: 最初にfalseになったら停止
a.take_while {|x| x < 3 }  # => [0, 1, 2]

# select: 全要素を評価して条件を満たすものを全て取得
a.select {|x| x < 3 }      # => [0, 1, 2]
```

**違いが現れる例**:

```ruby
a = [1, 2, 5, 1, 2]

a.take_while {|x| x < 3 }  # => [1, 2]  ← 5で停止
a.select {|x| x < 3 }      # => [1, 2, 1, 2]  ← 全て評価
```

### 実践的なユースケース

#### 1. ソート済み配列からの範囲抽出

```ruby
# ソート済み配列から特定範囲の要素を効率的に取得
sorted_numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
small_numbers = sorted_numbers.take_while {|n| n <= 5 }
# => [1, 2, 3, 4, 5]
```

#### 2. ログの先頭部分の取得

```ruby
# エラーが発生するまでのログエントリを取得
log_entries = [
  { status: 'success', message: 'Started' },
  { status: 'success', message: 'Processing' },
  { status: 'error', message: 'Failed' },
  { status: 'success', message: 'Recovered' }
]

successful_entries = log_entries.take_while {|entry| entry[:status] == 'success' }
# => [{ status: 'success', message: 'Started' }, { status: 'success', message: 'Processing' }]
```

#### 3. 連続する同じ値の取得

```ruby
# 配列の先頭から連続する同じ値を取得
values = [5, 5, 5, 3, 2, 5, 5]
first_group = values.take_while {|v| v == values.first }
# => [5, 5, 5]
```

### パフォーマンスの考慮事項

**効率的な点**:
- ✅ 条件が満たされなくなった時点で即座に停止
- ✅ 不要な要素の評価をスキップ
- ✅ 大きな配列でも先頭部分だけの処理なら高速

**注意点**:
- ⚠️ 配列がソートされていない場合、意図しない結果になることがある
- ⚠️ 条件が一度も満たされない場合は空配列を返す

### Enumerator としての使用

ブロックを与えない場合、`Enumerator` を返します：

```ruby
a = [0, 1, 2, 3, 4, 5]
enum = a.take_while  # => #<Enumerator: ...>

# 後でブロックを与えて評価
result = enum.each {|x| x < 3 }  # => [0, 1, 2]
```

### まとめ

`take_while` は以下のような場合に最適です：

- ✅ 配列の先頭から条件に基づいて要素を取得したい
- ✅ 条件が満たされなくなったら即座に停止したい
- ✅ パフォーマンスを重視したい（不要な評価をスキップ）

全要素を評価する必要がある場合は `select` や `filter` を使用しましょう。
