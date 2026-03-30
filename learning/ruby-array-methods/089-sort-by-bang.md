# Array#sort_by!

- 日付: 2026-03-30
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-sort_by-21
- daily 記録: [daily/2026-03-30.md](../../daily/2026-03-30.md)

## 基本情報

```ruby
array.sort_by! { |element| ... } → self
array.sort_by! → Enumerator
```

**機能**: ブロックが与えられた場合、そのブロックを使用して配列の要素を破壊的にソートし、自身（self）を返します。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# With a block given, sorts the elements of self in place; returns self.
# ブロックが与えられた場合、そのブロックを使用して自身（self）の要素を破壊的にソートします。その後、自身を返します。
# Calls the block with each successive element; sorts elements based on the values returned from the block:
# 各要素に対してブロックを呼び出し、その戻り値に基づいて要素を並べ替えます。
a = ['aaaa', 'bbb', 'cc', 'd']
a.sort_by! {|element| element.size }
a # => ["d", "cc", "bbb", "aaaa"]

# For duplicate values returned by the block, the ordering is indeterminate, and may be unstable.
# ブロックから返される値が重複している場合、その順序は不定であり、不安定になる可能性があります。
# With no block given, returns a new Enumerator.
# Related: see Methods for Assigning.
```

## 深掘り・補足

### 破壊的メソッドの特徴

`sort_by!` は破壊的メソッド（`!` 付き）で、元の配列を直接変更します：

```ruby
words = ['apple', 'pie', 'zoo', 'at']
words.sort_by! { |word| word.length }
words # => ["at", "pie", "zoo", "apple"]
# 元の配列が変更される
```

**非破壊的な `sort_by` との違い**：

```ruby
words = ['apple', 'pie', 'zoo', 'at']
sorted_words = words.sort_by { |word| word.length }
# words は変更されない: ["apple", "pie", "zoo", "at"]
# sorted_words は新しい配列: ["at", "pie", "zoo", "apple"]
```

### 重要なポイント

#### 1. ブロックの戻り値でソート

ブロックから返される値（数値、文字列など）を比較してソートします：

```ruby
# 文字列の長さでソート
['aaaa', 'bbb', 'cc', 'd'].sort_by! { |s| s.size }
# => ["d", "cc", "bbb", "aaaa"]

# 数値の絶対値でソート
[-5, 3, -1, 7, -9].sort_by! { |n| n.abs }
# => [-1, 3, -5, 7, -9]

# オブジェクトの属性でソート
users.sort_by! { |user| user.created_at }
```

#### 2. 安定ソートではない

同じ値の要素の順序は保証されません：

```ruby
words = ['cat', 'dog', 'ant', 'bee']
words.sort_by! { |w| w.length }
# => ["cat", "dog", "ant", "bee"] または ["dog", "cat", "bee", "ant"]
# 3文字の単語（cat, dog, ant, bee）の順序は不定
```

順序を保ちたい場合は、追加の条件を使います：

```ruby
words.sort_by!.with_index { |w, i| [w.length, i] }
# => 元のインデックスも考慮して安定ソート
```

#### 3. ブロックなしの場合

ブロックを渡さない場合、Enumerator を返します：

```ruby
enum = ['aaaa', 'bbb', 'cc'].sort_by!
# => #<Enumerator: ...>

enum.with_index { |s, i| [s.size, i] }
# => 長さとインデックスでソート
```

### 使用例

#### 複雑な条件でのソート

```ruby
# ハッシュの配列を複数キーでソート
users = [
  { name: 'Alice', age: 30 },
  { name: 'Bob', age: 25 },
  { name: 'Charlie', age: 30 }
]

# 年齢の昇順、同じ年齢なら名前のアルファベット順
users.sort_by! { |u| [u[:age], u[:name]] }
# => [
#   { name: 'Bob', age: 25 },
#   { name: 'Alice', age: 30 },
#   { name: 'Charlie', age: 30 }
# ]
```

#### 降順ソート

```ruby
# マイナスを付けて降順に
numbers = [3, 1, 4, 1, 5]
numbers.sort_by! { |n| -n }
# => [5, 4, 3, 1, 1]

# 文字列の場合は reverse を使う
words = ['apple', 'pie', 'zoo']
words.sort_by! { |w| w.reverse }
# 逆アルファベット順
```

### 関連メソッド

- **[088. sort](088-sort.md)**: 直接比較でソート（`<=>` を使用）
- `sort_by`: 非破壊的なバージョン
- `reverse!`: ソート後に反転する場合

### パフォーマンスの考慮事項

#### `sort` vs `sort_by!`

`sort_by!` は **Schwartzian transform** を使用し、ブロックを各要素に対して1回だけ実行します：

```ruby
# sort を使った場合（非効率）
words.sort! { |a, b| a.length <=> b.length }
# => ブロックが比較のたびに実行される（O(n log n) 回）

# sort_by! を使った場合（効率的）
words.sort_by! { |w| w.length }
# => ブロックが各要素に1回だけ実行される（O(n) 回）
```

**ベストプラクティス**：
- ブロック内で計算コストが高い場合は `sort_by!` を使う
- 単純な比較なら `sort!` でも OK

#### メモリ使用量

`sort_by!` は一時的なメモリを使用します：

```ruby
# 内部的には [元の要素, ソートキー] のペアを作成
# ソート後に元の配列を書き換える
```

超大規模な配列では、メモリ使用量に注意が必要です。

### 注意点

1. **破壊的メソッド**: 元の配列が変更されるので、必要に応じてコピーを保持
2. **戻り値は self**: メソッドチェーンが可能
3. **不安定ソート**: 同じキーの要素の順序は保証されない
4. **ブロック必須**: ブロックなしでは Enumerator を返すだけ
