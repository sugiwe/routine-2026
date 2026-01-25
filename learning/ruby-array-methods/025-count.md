# Array#count

- 日付: 2026-01-25
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-count

## 基本情報

```ruby
count → integer
count(object) → integer
count {|element| ... } → integer
```

指定された要素の数を返すメソッドです。Enumerable モジュールから継承されており、配列以外の列挙可能なオブジェクトでも使用できます。

「Methods for Querying」カテゴリに分類されており、配列の内容を調べるためのメソッドの一つです。

## daily からの記録

### メモ

（ユーザーからのメモなし）

### コード例・補足

```ruby
# Returns a count of specified elements.
# 指定された要素の数を返します。
# With no argument and no block, returns the count of all elements:
# 引数やブロックなしで呼び出された場合、すべての要素の数を返します。
[0, :one, 'two', 3, 3.0].count # => 5

# With argument object given, returns the count of elements == to object:
# 与えられたオブジェクトと比較して、等しい要素の数を返します。
[0, :one, 'two', 3, 3.0].count(3) # => 2

# With no argument and a block given, calls the block with each element; returns the count of elements for which the block returns a truthy value:
# 引数もブロックも指定されずに呼び出された場合、各要素に対してブロックを実行し、ブロックが真と評価される要素の数を返します。
[0, 1, 2, 3].count {|element| element > 1 } # => 2

# With argument object and a block given, issues a warning, ignores the block, and returns the count of elements == to object.
# 引数としてオブジェクトとブロックが与えられた場合、警告を発し、ブロックを無視して、オブジェクトと等しい要素の数を返します。
[0, 1, 2, 3].count(1) {|element| element > 1 }
# warning: given block not used
# => 1
```

## 深掘り・補足

### 3つの使い方

`count` メソッドには3つの使用パターンがあります：

#### 1. 引数なし・ブロックなし：全要素数を返す

```ruby
[1, 2, 3, 4, 5].count  # => 5
```

配列全体の要素数を取得します。`length` や `size` と同じ結果になりますが、count は Enumerable から来ているため、より汎用的です。

#### 2. 引数あり：特定の値と等しい要素をカウント

```ruby
[1, 2, 3, 2, 4, 2].count(2)  # => 3
```

`==` 演算子を使って等価性を判定するため、異なる型でも等しいとみなされる場合があります：

```ruby
[1, 2, 3, 3.0].count(3)    # => 2  (3 と 3.0 は == で等しい)
```

#### 3. ブロックあり：条件を満たす要素をカウント

```ruby
[1, 2, 3, 4, 5].count { |n| n.even? }  # => 2
```

ブロックが真を返す要素のみをカウントします。複雑な条件でフィルタリングしたい場合に便利です。

### length/size との違い

- **`length` / `size`**：配列の要素数を O(1) で返す（配列内部で保持している値を返すだけ）
- **`count`**：要素を走査して数える（引数やブロックがない場合でも Enumerable の実装を使う）

パフォーマンス重視で単純な要素数を取得したい場合は `length` や `size` を使う方が良いです。

### 注意点

#### 引数とブロックを同時に指定した場合

```ruby
[1, 2, 3].count(2) { |n| n > 1 }
# warning: given block not used
# => 1
```

引数が優先され、ブロックは無視されます（警告が出る）。これは意図しない動作を防ぐための仕様です。

#### nil のカウント

```ruby
[1, nil, 2, nil, 3].count(nil)  # => 2
```

`nil` も通常の値として扱われ、正しくカウントされます。

### 実用例

#### 条件に合う要素の割合を計算

```ruby
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
even_count = numbers.count(&:even?)
total = numbers.length
percentage = (even_count.to_f / total * 100).round(1)
# => 50.0%
```

#### 重複チェック

```ruby
items = ['apple', 'banana', 'apple', 'orange']
items.count('apple') > 1  # => true (重複がある)
```

### 関連メソッド

- `length` / `size`：要素数を取得（高速）
- `empty?`：配列が空かどうかを判定
- `include?`：特定の要素が含まれるかを判定
- `select`：条件に合う要素を抽出した新しい配列を返す
- `tally`：各要素の出現回数をハッシュで返す（Ruby 2.7+）
