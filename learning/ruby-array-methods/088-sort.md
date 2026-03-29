# Array#sort

- 日付: 2026-03-29
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-sort
- daily 記録: [daily/2026-03-29.md](../../daily/2026-03-29.md)

## 基本情報

```ruby
sort -> new_array
sort {|a, b| ... } -> new_array
```

`self` の要素をソートした新しい配列を返します。元の配列は変更されません（非破壊的メソッド）。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# Returns a new array containing the elements of self, sorted.
# self の要素をソートした、新しい配列を返します。
# With no block given, compares elements using operator <=> (see Object#<=>):
# ブロックが与えられない場合、演算子 <=> を使用して要素を比較します（Object#<=> を参照）。
[0, 2, 3, 1].sort # => [0, 1, 2, 3]

# With a block given, calls the block with each combination of pairs of elements from self; for each pair a and b, the block should return a numeric:
# ブロックが与えられた場合、selfの要素のすべてのペアの組み合わせに対してブロックを呼び出します。各ペア（a, b）に対して、ブロックは数値を返す必要があります。

# - Negative when b is to follow a.
# - aの後にbが続く場合は負の数。
# - Zero when a and b are equivalent.
# - aとbが等しい場合はゼロ。
# - Positive when a is to follow b.
# - bの後にaが続く場合は正の数。

# Example:

a = [3, 2, 0, 1]
a.sort {|a, b| a <=> b } # => [0, 1, 2, 3]
a.sort {|a, b| b <=> a } # => [3, 2, 1, 0]

# When the block returns zero, the order for a and b is indeterminate, and may be unstable.
# ブロックがゼロを返す場合、aとbの順序は不定となり、不安定になる可能性があります。
```

## 深掘り・補足

### 重要なポイント

#### 1. 非破壊的メソッド

`sort` は新しい配列を返し、元の配列は変更されません。元の配列自体をソートしたい場合は `sort!` を使用します。

```ruby
arr = [3, 1, 2]
sorted = arr.sort  # => [1, 2, 3]
arr                # => [3, 1, 2] (元の配列は変更されていない)
```

#### 2. デフォルトのソート（`<=>` 演算子）

ブロックを指定しない場合、`<=>` 演算子（spaceship operator）を使って要素を比較します。

- 数値: 小さい順
- 文字列: 辞書順（アルファベット順）

```ruby
[3, 1, 4, 1, 5].sort           # => [1, 1, 3, 4, 5]
['banana', 'apple', 'cherry'].sort  # => ["apple", "banana", "cherry"]
```

#### 3. カスタムソート（ブロックを使用）

ブロックを渡すことで、独自の比較ロジックを実装できます。ブロックは2つの要素 `a` と `b` を受け取り、以下の値を返す必要があります：

- **負の数**: `a` が `b` より前に来る
- **0**: `a` と `b` は等価（順序は不定）
- **正の数**: `a` が `b` より後に来る

```ruby
# 降順ソート
[1, 3, 2].sort {|a, b| b <=> a }  # => [3, 2, 1]

# 文字列の長さでソート
words = ['apple', 'pie', 'banana']
words.sort {|a, b| a.length <=> b.length }  # => ["pie", "apple", "banana"]
```

### 実践的なユースケース

#### ケース1: 複雑なオブジェクトのソート

```ruby
users = [
  { name: 'Alice', age: 30 },
  { name: 'Bob', age: 25 },
  { name: 'Charlie', age: 35 }
]

# 年齢でソート
users.sort {|a, b| a[:age] <=> b[:age] }
# => [{:name=>"Bob", :age=>25}, {:name=>"Alice", :age=>30}, {:name=>"Charlie", :age=>35}]
```

#### ケース2: 大文字小文字を区別しないソート

```ruby
words = ['Banana', 'apple', 'Cherry']
words.sort {|a, b| a.downcase <=> b.downcase }
# => ["apple", "Banana", "Cherry"]
```

### 注意点

#### 安定性について

ブロックが `0` を返す場合、同等の要素の順序は保証されません（不安定ソート）。順序を保証したい場合は、追加の条件を加える必要があります。

```ruby
# 不安定な例
data = [{name: 'A', priority: 1}, {name: 'B', priority: 1}]
data.sort {|a, b| a[:priority] <=> b[:priority] }
# => 順序は不定（AとBの順序が保証されない）
```

### パフォーマンスの考慮事項

- **時間計算量**: O(n log n)
- ブロックを使用する場合、ブロック内の処理が効率的であることを確認してください
- 同じ基準で繰り返しソートする場合は、`sort_by` の方が効率的な場合があります

```ruby
# sort を使った場合（非効率）
words.sort {|a, b| a.length <=> b.length }

# sort_by を使った場合（効率的）
words.sort_by {|word| word.length }
```

### 関連メソッド

- **`sort!`**: 破壊的ソート（元の配列を変更）
- **`sort_by`**: ブロックの戻り値でソート（より効率的）
- **`sort_by!`**: `sort_by` の破壊的版
- **`reverse`**: 配列を逆順にする
- **`shuffle`**: 配列をランダムにシャッフル

### 関連する演算子

- **`<=>`**: spaceship operator（比較演算子）
  - `a < b` なら負の数
  - `a == b` なら 0
  - `a > b` なら正の数
