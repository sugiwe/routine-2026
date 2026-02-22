# Array#inspect

- 日付: 2026-02-22
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-inspect
- daily 記録: [daily/2026-02-22.md](../../daily/2026-02-22.md)

## 基本情報

```ruby
inspect → new_string
```

配列の各要素に対して `inspect` メソッドを呼び出して生成される、新しい文字列を返します。

**エイリアス**: `to_s`

## ユーザーの学習記録（daily より）

### 第一印象のメモ

`inject` と勘違いしていた、`inspect` は `to_s` のエイリアス

### コード例

```ruby
# inspect → new_string
# Returns the new string formed by calling method inspect on each array element:
# 配列の各要素に対してinspectメソッドを呼び出して生成される、新しい文字列を返します。
a = [:foo, 'bar', 2]
a.inspect # => "[:foo, \"bar\", 2]"

# Related: see Methods for Converting.
# Also aliased as: to_s
```

## 深掘り・補足

### `inspect` と `to_s` の関係

Array クラスでは、`inspect` と `to_s` は同じ動作をします（`to_s` は `inspect` のエイリアス）。しかし、他のクラスでは異なる場合があります：

```ruby
# Arrayでは同じ
arr = [1, 2, 3]
arr.inspect  # => "[1, 2, 3]"
arr.to_s     # => "[1, 2, 3]"

# 他のクラスでは異なる場合も
# 例えば String クラスでは挙動が異なる
str = "hello"
str.inspect  # => "\"hello\""
str.to_s     # => "hello"

# 一方、HashではArrayと同様に同じ結果になる
hash = {a: 1, b: 2}
hash.inspect  # => "{:a=>1, :b=>2}"
hash.to_s     # => "{:a=>1, :b=>2}"
```

### デバッグでの活用

`inspect` の最大の用途はデバッグです。各要素の型情報を保持した形で表示されるため、配列の内容を正確に把握できます：

```ruby
# 文字列と数値とシンボルが混在
mixed = ["hello", 123, :symbol, nil]
puts mixed.inspect
# => ["hello", 123, :symbol, nil]

# 要素の型が視覚的に分かる
# - 文字列はダブルクォートで囲まれる
# - シンボルは先頭にコロン
# - nilはそのまま表示
```

### ネストした配列での動作

```ruby
nested = [1, [2, 3], [[4, 5], 6]]
nested.inspect
# => "[1, [2, 3], [[4, 5], 6]]"

# 各要素のinspectが再帰的に呼ばれる
```

### `p` メソッドとの関係

Ruby の `p` メソッドは内部的に `inspect` を使用します：

```ruby
arr = [:foo, 'bar', 2]

# この2つは同じ出力
p arr          # => [:foo, "bar", 2]
puts arr.inspect  # => [:foo, "bar", 2]

# putsだけだと異なる（要素が改行区切りで表示）
puts arr
# =>
# foo
# bar
# 2
```

### 他の変換メソッドとの比較

```ruby
arr = [:foo, 'bar', 2]

# inspect: デバッグ向け、型情報を保持
arr.inspect
# => "[:foo, \"bar\", 2]"

# join: 要素を連結した文字列
arr.join(', ')
# => "foo, bar, 2"

# to_s: inspectと同じ（エイリアス）
arr.to_s
# => "[:foo, \"bar\", 2]"
```

### パフォーマンスと注意点

#### 大規模配列での注意

```ruby
# 大量の要素を持つ配列
large = (1..10000).to_a
large.inspect  # 非常に長い文字列が生成される
```

ログ出力やデバッグ時に大規模配列を `inspect` すると、出力が膨大になる可能性があります。

#### 循環参照への対応

```ruby
arr = [1, 2, 3]
arr << arr  # 自分自身を追加

arr.inspect
# => "[1, 2, 3, [...]]"
# 循環参照は [...] で表現される
```

Ruby は循環参照を検出し、無限ループを防ぎます。

### 実践的なユースケース

#### ログ出力

```ruby
def process_items(items)
  puts "Processing: #{items.inspect}"
  # 処理...
end
```

#### テストでのデバッグ

```ruby
# RSpecなどでの期待値確認
expect(result.inspect).to eq('[1, 2, 3]')
```

#### 文字列補完での使用

```ruby
users = ['Alice', 'Bob']
message = "Users: #{users.inspect}"
# => "Users: [\"Alice\", \"Bob\"]"
```

### まとめ

- `inspect` は配列を人間が読みやすい文字列に変換
- `to_s` のエイリアスだが、他のクラスでは異なる場合がある
- デバッグやログ出力で非常に有用
- 各要素の型情報を保持した形で表示
- `p` メソッドは内部的に `inspect` を使用
- 大規模配列や循環参照に注意
