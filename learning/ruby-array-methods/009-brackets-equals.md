# Array#[]=

- 日付: 2026-01-09
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-5B-5D-3D

## 基本情報

```ruby
self[index] = object → object
self[start, length] = object → object
self[range] = object → object
```

与えられたオブジェクトに基づいて、自身の要素を割り当てます。オブジェクトを返します。

## メモ（daily より）

```ruby
# Assigns elements in self, based on the given object; returns object.
# 与えられたオブジェクトに基づいて、自身の要素を割り当てます。オブジェクトを返します。

a_orig = [:foo, 'bar', 2]

# With argument index.
# 引数インデックス付き
a = a_orig.dup # dupはオブジェクトの内容をコピーするメソッド
a[0] = 'foo' # => "foo"
a # => ["foo", "bar", 2]
a = a_orig.dup
a[7] = 'foo' # => "foo"
a # => [:foo, "bar", 2, nil, nil, nil, nil, "foo"]

# With arguments start and length.
a = a_orig.dup
a[0, 2] = 'foo' # => "foo"
a # => ["foo", 2]
a = a_orig.dup
# If start is non-negative and outside the array ( >= self.size), extends the array with nil, assigns object at offset start, and ignores length:
# 開始位置が非負で、配列の範囲外（self.size 以上）の場合、配列をnilで拡張し、指定されたオフセットにオブジェクトを割り当て、length は無視します。
a[6, 50] = 'foo' # => "foo"
a # => [:foo, "bar", 2, nil, nil, nil, "foo"]
# If length is zero, shifts elements at and following offset start and assigns object at offset start:
# length が 0 の場合、開始位置以降の要素をシフトし、開始位置にオブジェクトを割り当てます。
a[1, 0] = 'foo' # => "foo"
a # => [:foo, "foo", "bar", 2]

# With argument range.
a = a_orig.dup
a[0..1] = 'foo' # => "foo"
a # => ["foo", 2]
a = a_orig.dup
a[6..50] = 'foo' # => "foo"
a # => [:foo, "bar", 2, nil, nil, nil, "foo"]
```

## 深掘り・補足

### 3つの使い方の違い

`Array#[]=` は配列の要素を代入するメソッドで、引数の渡し方によって動作が大きく異なります。

#### 1. 単一インデックスによる代入：`self[index] = object`

**基本的な動作**
```ruby
arr = [:a, :b, :c]
arr[1] = :x
# => [:a, :x, :c]
```

**範囲外のインデックスへの代入**
```ruby
arr = [:a, :b, :c]
arr[5] = :x
# => [:a, :b, :c, nil, nil, :x]
```

配列のサイズを超えるインデックスに代入すると、**自動的に `nil` で埋められます**。これは便利な反面、意図しない `nil` が混入する原因にもなるので注意が必要です。

**負のインデックス**
```ruby
arr = [:a, :b, :c]
arr[-1] = :x  # 末尾の要素
# => [:a, :b, :x]
```

#### 2. 開始位置と長さによる代入：`self[start, length] = object`

**複数要素の置換**
```ruby
arr = [:a, :b, :c, :d, :e]
arr[1, 3] = [:x, :y]
# => [:a, :x, :y, :e]
```

- `start`: 開始位置（0-indexed）
- `length`: 置き換える要素の個数
- 右辺の配列の長さと `length` が異なっても OK（配列が伸縮する）

**length が 0 の場合：挿入動作**
```ruby
arr = [:a, :b, :c]
arr[1, 0] = [:x, :y]
# => [:a, :x, :y, :b, :c]
```

`length` が 0 の場合、要素を置き換えずに**挿入**します。これは `insert` メソッドに似た動作です。

**範囲外への代入**
```ruby
arr = [:a, :b, :c]
arr[5, 2] = :x
# => [:a, :b, :c, nil, nil, :x]
```

範囲外の開始位置を指定した場合、`length` は**無視**され、単純に末尾に追加されます（`nil` で埋められる）。

#### 3. 範囲による代入：`self[range] = object`

**範囲指定での置換**
```ruby
arr = [:a, :b, :c, :d, :e]
arr[1..3] = [:x, :y]
# => [:a, :x, :y, :e]
```

範囲（`Range`）を使った指定も可能です。`start, length` との違いは、範囲の**終端を明示的に指定できる**点です。

**排他的範囲（`...`）**
```ruby
arr = [:a, :b, :c, :d, :e]
arr[1...3] = [:x]  # インデックス1と2を置換（3は含まない）
# => [:a, :x, :d, :e]
```

### 重要な注意点

#### ⚠️ 1. 文字列を代入すると分解される

```ruby
arr = [:a, :b, :c]
arr[0, 2] = 'foo'
# => ["foo", :c]  ← 文字列がそのまま入る（単一要素の場合）

arr = [:a, :b, :c]
arr[0..1] = 'foo'
# => ["foo", :c]  ← 同様
```

実は、単一の文字列を代入した場合は文字列そのものが入りますが、**配列でない `Enumerable` を渡すと展開される**場合があります。

#### ⚠️ 2. 破壊的メソッド

`Array#[]=` は**破壊的メソッド**です。元の配列が変更されます。

```ruby
original = [:a, :b, :c]
copy = original
copy[1] = :x
original  # => [:a, :x, :c]  ← 元の配列も変わっている！
```

コピーを作りたい場合は `dup` や `clone` を使いましょう。

#### ⚠️ 3. 範囲外アクセスの違い

**読み取り（`[]`）** と **代入（`[]=`）** で挙動が異なります：

```ruby
arr = [:a, :b, :c]
arr[5]      # => nil（エラーにならない）
arr[5] = :x # => [:a, :b, :c, nil, nil, :x]（自動拡張）
```

### 実用的なユースケース

#### 1. データの部分更新

```ruby
data = [1, 2, 3, 4, 5]
# 中央の3つの要素を平均値で置き換える
data[1, 3] = [data[1..3].sum / 3]
# => [1, 3, 5]
```

#### 2. 配列の部分削除

```ruby
arr = [:a, :b, :c, :d, :e]
arr[1, 3] = []  # 空配列を代入 = 削除
# => [:a, :e]
```

#### 3. 条件付き更新

```ruby
results = [10, 20, 30, 40, 50]
# 30以下の値を0に置き換える
results.each_with_index do |val, i|
  results[i] = 0 if val <= 30
end
# => [0, 0, 0, 40, 50]
```

#### 4. 挿入操作（`length = 0` を利用）

```ruby
list = [:a, :c, :d]
# :bを挿入
list[1, 0] = [:b]
# => [:a, :b, :c, :d]
```

### 関連メソッド

- **`Array#insert`**: 挿入専用メソッド（より明示的）
- **`Array#delete_at`**: 特定インデックスの要素を削除
- **`Array#slice!`**: 範囲を削除して取り出す（破壊的）
- **`Array#[]()`**: 要素の読み取り（非破壊的）

### まとめ

`Array#[]=` は非常に柔軟なメソッドですが、その分挙動も複雑です：

- 単一要素の置換、複数要素の置換、挿入、削除まで可能
- 範囲外への代入は自動的に `nil` で埋められる
- 破壊的メソッドなので元の配列が変更される
- `length = 0` の場合は挿入動作になる

用途に応じて適切な形式を選び、意図しない動作を避けるために挙動を理解しておくことが重要です。