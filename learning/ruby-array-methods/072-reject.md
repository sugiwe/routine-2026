# Array#reject

- 日付: 2026-03-13
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-reject
- daily 記録: [daily/2026-03-13.md](../../daily/2026-03-13.md)

## 基本情報

```ruby
reject {|element| ... } → new_array
reject → new_enumerator
```

ブロックで指定された条件を**満たさない**要素を含む新しい配列を返します。元の配列は変更されません。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

（メモなし）

### コード例

```ruby
# With a block given, returns a new array whose elements are all those from self for which the block returns false or nil:
# ブロックが与えられた場合、ブロックがfalseまたはnilを返す要素をすべて除いた、新しい配列を返します。
a = [:foo, 'bar', 2, 'bat']
a1 = a.reject {|element| element.to_s.start_with?('b') }
a1 # => [:foo, 2]

# With no block given, returns a new Enumerator.
# Related: Methods for Fetching.
```

## 深掘り・補足

### `reject` の本質：フィルタリングの逆操作

`reject` は「ブロックが真を返す要素を**除外**する」メソッドです。つまり、条件を満たさない要素だけを集めた新しい配列を返します。

```ruby
numbers = [1, 2, 3, 4, 5, 6]
# 3より小さい数を除外（3以上だけ残す）
numbers.reject {|n| n < 3}  # => [3, 4, 5, 6]
```

### `select` との関係：完全な逆操作

`reject` と `select` は対になるメソッドです：

```ruby
arr = [1, 2, 3, 4, 5, 6]

# select: 条件を満たす要素を残す
arr.select {|n| n.even?}  # => [2, 4, 6]

# reject: 条件を満たす要素を除外する
arr.reject {|n| n.even?}  # => [1, 3, 5]
```

同じ結果を得るには、ブロック条件を逆にすればよい：

```ruby
arr.reject {|n| n.even?}    # => [1, 3, 5]
arr.select {|n| n.odd?}     # => [1, 3, 5]（同じ結果）
```

### 破壊的メソッド `reject!` との違い

```ruby
arr = [1, 2, 3, 4, 5]

# reject: 新しい配列を返す（元の配列は変更なし）
result = arr.reject {|n| n.even?}
result  # => [1, 3, 5]
arr     # => [1, 2, 3, 4, 5]（変更なし）

# reject!: 元の配列を直接変更
arr.reject! {|n| n.even?}
arr     # => [1, 3, 5]（破壊的変更）
```

### ブロックがない場合：Enumerator を返す

```ruby
enum = [1, 2, 3, 4].reject
enum.class  # => Enumerator

# Enumerator として後からブロックを渡せる
enum.each {|n| n.even?}  # => [1, 3]
```

### 実践的なユースケース

#### 1. nil / 空要素の除外

```ruby
data = ['Alice', nil, 'Bob', '', 'Charlie', nil]

# nil を除外
data.reject {|s| s.nil?}           # => ['Alice', 'Bob', '', 'Charlie']

# nil と空文字列を除外
data.reject {|s| s.nil? || s.empty?}  # => ['Alice', 'Bob', 'Charlie']
```

**注**: nil だけを除外する場合は `compact` が便利：

```ruby
data.compact  # => ['Alice', 'Bob', '', 'Charlie']
```

#### 2. 特定パターンの除外

```ruby
files = ['index.html', 'script.js', 'test.js', 'style.css']

# テストファイルを除外
files.reject {|f| f.include?('test')}  # => ['index.html', 'script.js', 'style.css']
```

#### 3. ハッシュのフィルタリング

```ruby
users = [
  {name: 'Alice', age: 25},
  {name: 'Bob', age: 17},
  {name: 'Charlie', age: 30}
]

# 未成年を除外
adults = users.reject {|u| u[:age] < 18}
# => [{name: 'Alice', age: 25}, {name: 'Charlie', age: 30}]
```

### パフォーマンスの考慮事項

- `reject` は新しい配列を生成するため、メモリを消費します
- 大きな配列で破壊的変更が許容できる場合は `reject!` を検討
- `lazy` と組み合わせて遅延評価も可能：

```ruby
(1..1_000_000).lazy.reject {|n| n.even?}.first(5)
# => [1, 3, 5, 7, 9]（全要素を処理せずに済む）
```

### まとめ

- **`reject`**: 条件を満たす要素を**除外**し、新しい配列を返す
- **`select` の逆操作**: 残したいものを指定するか、除外したいものを指定するかの違い
- **非破壊的**: 元の配列は変更されない（破壊的版は `reject!`）
- **用途**: 不要な要素を取り除く、フィルタリング、クリーンアップ処理など

「除外したい条件」が明確な場合は `reject` を、「残したい条件」が明確な場合は `select` を使うと、コードの意図が伝わりやすくなります。
