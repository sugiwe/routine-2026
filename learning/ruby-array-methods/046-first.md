# Array#first

- 日付: 2026-02-15
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-first
- daily 記録: [daily/2026-02-15.md](../../daily/2026-02-15.md)

## 基本情報

```ruby
first → object or nil
first(count) → new_array
```

配列の先頭要素を取得するメソッド。引数なしの場合は最初の要素を、引数ありの場合は先頭 n 個の要素を配列として返します。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# first → object or nil
# first(count) → new_array

# Returns elements from self, or nil; does not modify self.
# 自身を書き換えることなく、自身の要素を返すか、あるいはnilを返します。
# With no argument given, returns the first element (if available):
# 引数を指定しない場合、最初の要素を返します（存在する場合）。
a = [:foo, 'bar', 2]
a.first # => :foo
a # => [:foo, "bar", 2]

# If self is empty, returns nil.
# もし自身が空の場合、nilを返します。
[].first # => nil

# With a non-negative integer argument count given, returns the first count elements (as available) in a new array:
# 負でない整数値の`count`を引数として受け取り、最初の`count`個の要素を（利用可能な限り）新しい配列として返します。
a.first(0)  # => []
a.first(2)  # => [:foo, "bar"]
a.first(50) # => [:foo, "bar", 2]

# Related: see Methods for Querying.
```

## 深掘り・補足

### 重要なポイント

#### 1. 戻り値の型が引数によって変わる

これは**非常に重要**な特徴です：

```ruby
arr = [1, 2, 3]

# 引数なし → 要素そのものを返す（Integer など）
result1 = arr.first
result1         # => 1
result1.class   # => Integer

# 引数あり → 配列を返す（Array）
result2 = arr.first(1)
result2         # => [1]
result2.class   # => Array
```

**注意**: `arr.first` と `arr.first(1)` は異なる型を返すため、処理方法が変わります！

```ruby
# これは動かない
arr.first.each { |x| puts x }  # NoMethodError: undefined method `each' for 1:Integer

# これは動く
arr.first(1).each { |x| puts x }  # 1
```

#### 2. 空配列の扱い

```ruby
empty = []

# 引数なし → nil を返す
empty.first           # => nil

# 引数あり → 空配列を返す
empty.first(5)        # => []

# nil チェックが必要なケース
value = empty.first
value&.upcase  # => nil（safe navigation operator で安全に処理）
```

#### 3. 要求数が配列サイズを超える場合

```ruby
arr = [1, 2, 3]

# エラーにはならず、利用可能な全要素を返す
arr.first(10)   # => [1, 2, 3]

# 0 を指定すると空配列
arr.first(0)    # => []
```

### 実践的なユースケース

#### ケース 1: 最初の要素の確認

```ruby
users = User.order(:created_at).limit(10)

# 最初のユーザーを取得
first_user = users.first
puts "最古のユーザー: #{first_user.name}" if first_user
```

#### ケース 2: 先頭 n 件の処理

```ruby
logs = read_log_file()

# 最新 5 件のログを表示
logs.first(5).each do |log|
  puts log
end
```

#### ケース 3: デフォルト値との組み合わせ

```ruby
queue = []

# nil を返すので || でデフォルト値を設定可能
next_task = queue.first || "No tasks available"
```

#### ケース 4: パターンマッチング（Ruby 3.0+）

```ruby
case arr.first
in Integer => n
  puts "最初は数値: #{n}"
in String => s
  puts "最初は文字列: #{s}"
in nil
  puts "配列が空です"
end
```

### 関連メソッドとの比較

| メソッド | 動作 | 戻り値の型 | 空配列の場合 |
|----------|------|------------|--------------|
| `first` | 先頭要素 | 要素 or nil | nil |
| `first(n)` | 先頭 n 個 | Array | [] |
| `[0]` | インデックス 0 | 要素 or nil | nil |
| `take(n)` | 先頭 n 個 | Array（常に） | [] |
| `last` | 末尾要素 | 要素 or nil | nil |

#### `first` vs `[0]`

```ruby
arr = [10, 20, 30]

arr.first    # => 10
arr[0]       # => 10

# 動作はほぼ同じだが、意図が異なる
# first → 「最初の要素」という意味的な明確さ
# [0]   → 「インデックス 0」という技術的なアクセス
```

#### `first(n)` vs `take(n)`

```ruby
arr = [1, 2, 3, 4, 5]

arr.first(3)   # => [1, 2, 3]
arr.take(3)    # => [1, 2, 3]

# ほぼ同じだが、first(n) の方が一般的
# take は Enumerable モジュールのメソッドで、より汎用的
```

### 注意点

#### 1. 破壊的メソッドではない

```ruby
arr = [1, 2, 3]
arr.first       # => 1
arr             # => [1, 2, 3]（変更されない）

# 削除したい場合は shift を使う
arr.shift       # => 1
arr             # => [2, 3]（変更される）
```

#### 2. 負の引数はエラー

```ruby
arr = [1, 2, 3]

arr.first(-1)   # ArgumentError: negative array size
```

#### 3. nil との混同に注意

```ruby
arr = [nil, 1, 2]

arr.first       # => nil（要素が nil）
[].first        # => nil（配列が空）

# 区別が必要な場合
arr.empty?      # => false
arr.first.nil?  # => true
```

### パフォーマンス

`Array#first` は **O(1)** の時間複雑度です。配列の先頭へのアクセスは定数時間で行われます。

```ruby
# 1 要素でも 100 万要素でも同じ速度
small_arr = [1, 2, 3]
large_arr = (1..1_000_000).to_a

small_arr.first  # 高速
large_arr.first  # 同じく高速
```

### まとめ

- `first` は配列の先頭要素を取得する最も直感的な方法
- 引数なしと引数ありで**戻り値の型が異なる**点に注意
- 空配列の場合は `nil` または `[]` を返す
- 非破壊的メソッドなので元の配列は変更されない
- `[0]` や `take` との違いを理解して使い分ける

**使い分けの目安**:
- 最初の 1 要素だけ欲しい → `first`
- 最初の n 要素が欲しい → `first(n)` または `take(n)`
- 最初の要素を削除したい → `shift`
