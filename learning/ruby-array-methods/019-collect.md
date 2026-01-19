# Array#collect

- 日付: 2026-01-19
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-collect

## 基本情報

```ruby
collect {|element| ... } → new_array
collect → new_enumerator
```

`collect` は `map` メソッドのエイリアスです。配列の各要素に対してブロックを実行し、その戻り値からなる新しい配列を返します。

## daily からの記録

### メモ

（メモなし）

### コード例・補足

```ruby
# With a block given, calls the block with each element of self; returns a new array whose elements are the return values from the block:
# オブジェクトにブロックが与えられた場合、そのオブジェクトの各要素に対してブロックを実行し、ブロックの戻り値からなる新しい配列を返します。
a = [:foo, 'bar', 2]
a1 = a.map {|element| element.class }
a1 # => [Symbol, String, Integer]

# With no block given, returns a new Enumerator.
# ブロックが指定されない場合、新しいEnumeratorを返します。
# Related: collect!; see also Methods for Converting.
# Also aliased as: map
```

## 深掘り・補足

### 基本的な使い方

`collect` は配列の各要素を変換して新しい配列を生成するメソッドです。元の配列は変更されません（非破壊的メソッド）。

```ruby
# 数値を2倍にする
numbers = [1, 2, 3, 4, 5]
doubled = numbers.collect { |n| n * 2 }
# => [2, 4, 6, 8, 10]

# 文字列を大文字に変換
words = ['hello', 'world', 'ruby']
uppercased = words.collect { |word| word.upcase }
# => ["HELLO", "WORLD", "RUBY"]

# 要素の型を取得（daily の例と同様）
mixed = [:foo, 'bar', 2]
types = mixed.collect { |element| element.class }
# => [Symbol, String, Integer]
```

### `collect` vs `map`

`collect` と `map` は完全に同じメソッドです。どちらを使っても同じ結果が得られます。

```ruby
[1, 2, 3].collect { |n| n * 2 }  # => [2, 4, 6]
[1, 2, 3].map { |n| n * 2 }      # => [2, 4, 6]
```

**慣習的な使い分け**：
- Ruby コミュニティでは `map` の方が一般的に使われる
- 関数型プログラミングの文脈では `map` の方が馴染みやすい
- `collect` は Smalltalk 由来の名前

### ブロックなしで呼び出す場合

ブロックを渡さない場合、Enumerator オブジェクトが返されます。これにより、メソッドチェーンが可能になります。

```ruby
enum = [1, 2, 3].collect
# => #<Enumerator: [1, 2, 3]:collect>

# Enumerator を使ったメソッドチェーン
result = [1, 2, 3].collect.with_index { |n, i| n * i }
# => [0, 2, 6]
```

### 破壊的バージョン: `collect!`

元の配列自体を変更したい場合は、`collect!`（または `map!`）を使います。

```ruby
numbers = [1, 2, 3, 4, 5]
numbers.collect! { |n| n * 2 }
numbers  # => [2, 4, 6, 8, 10]（元の配列が変更される）
```

### 実用的なユースケース

#### 1. データの整形・抽出

```ruby
users = [
  { name: 'Alice', age: 30 },
  { name: 'Bob', age: 25 },
  { name: 'Charlie', age: 35 }
]

names = users.collect { |user| user[:name] }
# => ["Alice", "Bob", "Charlie"]
```

#### 2. 計算結果の配列生成

```ruby
prices = [100, 200, 300]
with_tax = prices.collect { |price| (price * 1.1).round }
# => [110, 220, 330]
```

#### 3. 条件付き変換

```ruby
numbers = [1, 2, 3, 4, 5]
result = numbers.collect { |n| n.even? ? n * 2 : n }
# => [1, 4, 3, 8, 5]
```

### 注意点

#### 1. 新しい配列を生成する

`collect` は常に新しい配列を生成します。元の配列は変更されません。

```ruby
original = [1, 2, 3]
result = original.collect { |n| n * 2 }
original  # => [1, 2, 3]（変更されていない）
result    # => [2, 4, 6]
```

#### 2. ブロックの戻り値がすべて使われる

ブロックの最後の式の評価結果が新しい配列の要素になります。`nil` も含まれます。

```ruby
[1, 2, 3].collect { |n| puts n }
# => [nil, nil, nil]（puts は nil を返すため）
```

#### 3. 副作用には注意

`collect` は変換のために使うべきで、副作用（ファイル書き込みなど）のために使うべきではありません。副作用が目的なら `each` を使いましょう。

```ruby
# 悪い例（副作用のために collect を使う）
[1, 2, 3].collect { |n| puts n }

# 良い例（副作用には each を使う）
[1, 2, 3].each { |n| puts n }
```

### パフォーマンスの考慮

- `collect` は新しい配列を生成するため、メモリを使用します
- 大きな配列を扱う場合は、必要に応じて `lazy` と組み合わせることでメモリ効率を改善できます

```ruby
# 巨大な配列の場合
(1..1_000_000).lazy.collect { |n| n * 2 }.first(5)
# => [2, 4, 6, 8, 10]
# lazy を使うことで、必要な分だけ計算される
```

### 関連メソッド

- **`map`**: `collect` の完全なエイリアス
- **`collect!` / `map!`**: 破壊的バージョン（元の配列を変更）
- **`select`**: 条件に合う要素だけを抽出（変換はしない）
- **`reject`**: 条件に合わない要素を抽出
- **`filter_map`**: `select` と `map` を組み合わせた効率的なメソッド（Ruby 2.7+）

```ruby
# filter_map の例
[1, 2, 3, 4, 5].filter_map { |n| n * 2 if n.even? }
# => [4, 8]
```

### まとめ

`collect` (= `map`) は、配列の各要素を変換して新しい配列を生成する基本的かつ強力なメソッドです。関数型プログラミングの中核をなすメソッドであり、Ruby で配列を扱う際に最も頻繁に使用されるメソッドの一つです。変換が目的の場合は `collect`/`map`、副作用が目的の場合は `each` を使い分けることが重要です。
