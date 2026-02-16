# Array#flatten

- 日付: 2026-02-16
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-flatten
- daily 記録: [daily/2026-02-16.md](../../daily/2026-02-16.md)

## 基本情報

```ruby
flatten(depth = nil) → new_array
```

ネストされた配列を平坦化（フラット化）して、指定された深さまで再帰的に展開した新しい配列を返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

どういう時に使うんだろう？

### コード例

```ruby
# flatten(depth = nil) → new_array

# Returns a new array that is a recursive flattening of self to depth levels of recursion; depth must be an integer-convertible object or nil. At each level of recursion:
# 自身を再帰的に平坦化し、指定された深さ（`depth`）まで展開した新しい配列を返します。`depth`は整数に変換できるオブジェクトか`nil`である必要があります。再帰の各レベルでは、以下の処理が行われます。
# - Each element that is an array is "flattened" (that is, replaced by its individual array elements).
# - 配列である要素はそれぞれ「フラット化」されます（つまり、個々の配列要素に置き換えられます）。
# - Each element that is not an array is unchanged (even if the element is an object that has instance method flatten).
# - 配列ではない要素は（たとえインスタンスメソッドflattenを持つオブジェクトであっても）変更されません。

# With non-negative integer argument depth, flattens recursively through depth levels:
# 負でない整数引数 `depth` を指定すると、`depth` のレベルまで再帰的に平坦化します。
require 'set'

a = [ 0, [ 1, [2, 3], 4 ], 5, {foo: 0}, Set.new([6, 7]) ]
a              # => [0, [1, [2, 3], 4], 5, {:foo=>0}, #<Set: {6, 7}>]
a.flatten(0)   # => [0, [1, [2, 3], 4], 5, {:foo=>0}, #<Set: {6, 7}>]
a.flatten(1  ) # => [0, 1, [2, 3], 4, 5, {:foo=>0}, #<Set: {6, 7}>]
a.flatten(1.1) # => [0, 1, [2, 3], 4, 5, {:foo=>0}, #<Set: {6, 7}>]
a.flatten(2)   # => [0, 1, 2, 3, 4, 5, {:foo=>0}, #<Set: {6, 7}>]
a.flatten(3)   # => [0, 1, 2, 3, 4, 5, {:foo=>0}, #<Set: {6, 7}>]

# With nil or negative depth, flattens all levels.

a.flatten     # => [0, 1, 2, 3, 4, 5, {:foo=>0}, #<Set: {6, 7}>]
a.flatten(-1) # => [0, 1, 2, 3, 4, 5, {:foo=>0}, #<Set: {6, 7}>]
```

## 深掘り・補足

### どういう時に使うのか？

`flatten` は、多次元配列を一次元配列に変換したい時に非常に便利です。具体的なユースケースを見ていきましょう。

### 実践的なユースケース

#### 1. データ処理での利用

```ruby
# 複数のカテゴリからアイテムを取得して、一つのリストにまとめる
categories = [
  ["apple", "banana"],
  ["carrot", "lettuce"],
  ["chicken", "beef"]
]

all_items = categories.flatten
# => ["apple", "banana", "carrot", "lettuce", "chicken", "beef"]
```

#### 2. ネストしたループの結果を平坦化

```ruby
# 各ユーザーの購入履歴を一つのリストにまとめる
users = [
  { name: "Alice", purchases: ["book", "pen"] },
  { name: "Bob", purchases: ["notebook"] },
  { name: "Carol", purchases: ["pencil", "eraser"] }
]

all_purchases = users.map { |user| user[:purchases] }.flatten
# => ["book", "pen", "notebook", "pencil", "eraser"]
```

#### 3. 階層データの処理

```ruby
# ファイルツリー構造から全てのファイル名を取得
file_tree = [
  "README.md",
  ["src", ["main.rb", "helper.rb"]],
  ["test", ["test_main.rb"]]
]

all_files = file_tree.flatten
# => ["README.md", "src", "main.rb", "helper.rb", "test", "test_main.rb"]
```

### 深さ（depth）パラメータの活用

#### 部分的な平坦化

```ruby
# レベル1だけ平坦化したい場合
data = [[1, 2], [3, [4, 5]], [6, 7]]
data.flatten(1)
# => [1, 2, 3, [4, 5], 6, 7]
# 最外層のネストだけが解除される
```

これは、特定の階層構造を保ちたい場合に便利です。

### 重要な注意点

#### 1. 配列以外のオブジェクトは平坦化されない

```ruby
require 'set'

a = [1, [2, 3], {foo: 0}, Set.new([4, 5])]
a.flatten
# => [1, 2, 3, {:foo=>0}, #<Set: {4, 5}>]
```

Hash や Set などは配列ではないので、そのまま残ります。

#### 2. 破壊的メソッド `flatten!` との違い

```ruby
arr = [[1, 2], [3, 4]]

# 非破壊的（元の配列は変更されない）
flattened = arr.flatten
arr  # => [[1, 2], [3, 4]] (変更なし)

# 破壊的（元の配列が変更される）
arr.flatten!
arr  # => [1, 2, 3, 4] (変更された)
```

#### 3. パフォーマンスの考慮

大きなネストした配列を平坦化する場合、深さを指定することでパフォーマンスを改善できます：

```ruby
# 全レベルを平坦化（より重い処理）
deep_array.flatten

# 必要な深さだけ平坦化（より軽い処理）
deep_array.flatten(1)
```

### よくあるパターン

#### map と組み合わせた使用

```ruby
# flat_map の代わりに map + flatten を使うパターン
[1, 2, 3].map { |x| [x, x * 2] }.flatten
# => [1, 2, 2, 4, 3, 6]

# Ruby 2.5+ では flat_map を使う方が効率的
[1, 2, 3].flat_map { |x| [x, x * 2] }
# => [1, 2, 2, 4, 3, 6]
```

### まとめ

`flatten` は、複雑にネストした配列構造を扱う際の強力なツールです。データ処理、コレクション操作、階層データの変換など、様々な場面で活躍します。深さパラメータを適切に使うことで、必要な階層だけを平坦化できる柔軟性も持っています。
