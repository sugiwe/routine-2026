# Array#reverse_each

- 日付: 2026-03-18
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-reverse_each
- daily 記録: [daily/2026-03-18.md](../../daily/2026-03-18.md)

## 基本情報

```ruby
reverse_each {|element| ... } → self
reverse_each → Enumerator
```

配列の要素を**後ろから順に**繰り返し処理するメソッドです。元の配列は変更されません。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

説明見ただけでは全然わからない、`reverse`と何が違うんだろう？と思って調べた。`reverse`は配列を反転させるもので、`reverse_each`は配列を後ろからループさせるものらしい。

### コード例

```ruby
# When a block given, iterates backwards over the elements of self, passing, in reverse order, each element to the block; returns self:
# ブロックが与えられた場合、自身の要素を後方から繰り返し処理し、各要素を逆順でブロックに渡します。そして自身を返します。
a = []
[0, 1, 2].reverse_each {|element| a.push(element) }
a # => [2, 1, 0]

# Allows the array to be modified during iteration:
# 繰り返し処理中に配列の変更を許可します。
a = ['a', 'b', 'c']
a.reverse_each {|element| a.clear if element.start_with?('b') }
a # => []

# When no block given, returns a new Enumerator.
# Related: see Methods for Iterating.
```

## 深掘り・補足

### reverse と reverse_each の違い

ユーザーの疑問「`reverse` と何が違うの？」への答え：

#### `reverse` メソッド
```ruby
arr = [1, 2, 3]
reversed = arr.reverse  # => [3, 2, 1]
arr                     # => [1, 2, 3]（元の配列は変わらない）
```
- **配列を反転した新しい配列を返す**
- 新しいメモリ領域に配列を作成
- 戻り値: 反転された新しい配列

#### `reverse_each` メソッド
```ruby
arr = [1, 2, 3]
arr.reverse_each { |x| puts x }  # 3, 2, 1 と出力
# => [1, 2, 3]（self を返す）
arr                              # => [1, 2, 3]（元の配列は変わらない）
```
- **配列を後ろから順に繰り返し処理する**
- 新しい配列は作成しない（メモリ効率が良い）
- 戻り値: 元の配列（self）

### 重要なポイント

#### 1. メモリ効率
```ruby
# メモリ効率が悪い書き方
[1, 2, 3].reverse.each { |x| puts x }  # 新しい配列を作成してから繰り返す

# メモリ効率が良い書き方
[1, 2, 3].reverse_each { |x| puts x }  # 新しい配列を作成せずに繰り返す
```

大きな配列の場合、`reverse_each` を使うことでメモリ消費を抑えられます。

#### 2. 戻り値の違い
```ruby
result1 = [1, 2, 3].reverse           # => [3, 2, 1]（新しい配列）
result2 = [1, 2, 3].reverse_each { }  # => [1, 2, 3]（元の配列）
```

`reverse_each` は元の配列（self）を返すので、メソッドチェーンで使う際は注意が必要です。

#### 3. Enumerator を返す
```ruby
enum = [1, 2, 3].reverse_each  # Enumerator を返す
enum.next  # => 3
enum.next  # => 2
enum.next  # => 1
```

ブロックを渡さない場合、Enumerator を返すので、遅延評価や他の Enumerator メソッドと組み合わせて使えます。

### ユースケース

#### 1. 後ろから順に処理したいとき
```ruby
# ログファイルを新しい順に表示
log_entries.reverse_each do |entry|
  puts "#{entry.timestamp}: #{entry.message}"
end
```

#### 2. スタック的な操作
```ruby
# スタックから取り出すイメージで処理
stack = [1, 2, 3, 4, 5]
stack.reverse_each do |item|
  puts "Processing: #{item}"
end
```

#### 3. メモリを節約したい大規模データ
```ruby
# 大量のデータを逆順で処理する場合
large_array.reverse_each do |item|
  # reverse を使うと新しい配列のメモリが必要
  # reverse_each なら元の配列だけで済む
  process(item)
end
```

### 注意点

#### 繰り返し処理中の配列変更
```ruby
a = ['a', 'b', 'c']
a.reverse_each {|element| a.clear if element.start_with?('b') }
a # => []
```

ユーザーのコード例にもある通り、繰り返し処理中に配列を変更することは**可能**ですが、予期しない動作を引き起こす可能性があるため、通常は避けるべきです。

### まとめ

- **`reverse`**: 配列を反転した新しい配列を作る（配列が欲しい場合）
- **`reverse_each`**: 配列を後ろから繰り返し処理する（繰り返しが目的の場合）

**どちらを使うべきか？**
- 反転した配列が必要 → `reverse`
- 単に後ろから処理したい → `reverse_each`（メモリ効率が良い）

```ruby
# 配列が欲しい場合
reversed = arr.reverse

# 繰り返し処理だけしたい場合
arr.reverse_each { |x| puts x }
```
