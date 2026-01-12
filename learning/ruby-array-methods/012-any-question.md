# Array#any?

- 日付: 2026-01-12
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-any-3F

## 基本情報

```ruby
any? → true or false
any?(object) → true or false
any? {|element| ... } → true or false
```

配列内のいずれかの要素について、指定された基準が満たされるかどうかを返します。

## daily からの記録

### メモ

（メモなし）

### コード例・補足

```ruby
# Returns whether for any element of self, a given criterion is satisfied.
# selfのいずれかの要素について、指定された基準が満たされるかどうかを返します。
# With no block and no argument, returns whether any element of self is truthy:
# ブロックも引数もない場合、selfのいずれかの要素が真（truthy）であるかどうかを返します。
[nil, false, []].any? # => true  # Array object is truthy.
[nil, false, {}].any? # => true  # Hash object is truthy.
[nil, false, ''].any? # => true  # String object is truthy.
[nil, false].any?     # => false # Nil and false are not truthy.

# With argument object given, returns whether object === ele for any element ele in self:
# 引数としてオブジェクトが与えられた場合、self のいずれかの要素 ele に対して object === ele であるかどうかを返します:
[nil, false, 0].any?(0)          # => true
[nil, false, 1].any?(0)          # => false
[nil, false, 'food'].any?(/foo/) # => true
[nil, false, 'food'].any?(/bar/) # => false

# With a block given, calls the block with each element in self; returns whether the block returns any truthy value:
# ブロックが与えられた場合、selfの各要素に対してブロックを呼び出します。ブロックが真となる値 (truthy value) を返したかどうかを返します。
[0, 1, 2].any? {|ele| ele < 1 } # => true
[0, 1, 2].any? {|ele| ele < 0 } # => false

# With both a block and argument object given, ignores the block and uses object as above.
# Special case: returns false if self is empty (regardless of any given argument or block).
# ブロックと引数オブジェクトの両方が与えられた場合、ブロックは無視され、上記のようにオブジェクトが使用されます。
# 特殊なケース: self が空の場合、(与えられた引数やブロックに関わらず) false を返します。
```

## 深掘り・補足

### 重要なポイント

`any?` は、配列内の「**少なくとも1つの要素が条件を満たすか**」を判定する述語メソッドです。英語の "any"（いずれか）という意味そのままの動作をします。

### 3つの使用パターン

#### 1. 引数もブロックもなし：truthy 判定

```ruby
[nil, false, []].any?   # => true  ([] は truthy)
[nil, false].any?       # => false (すべて falsy)
[].any?                 # => false (空配列)
```

Ruby では `nil` と `false` だけが falsy で、**それ以外はすべて truthy** です。空配列 `[]`、空文字列 `''`、数値の `0` もすべて truthy として扱われます。

#### 2. 引数あり：case equality (`===`) による判定

```ruby
[1, 2, 3].any?(Integer)  # => true
[1, 2, 3].any?(String)   # => false
['apple', 'banana'].any?(/app/)  # => true
[10, 20, 30].any?(15..25)        # => true (20 が範囲内)
```

引数が与えられた場合、`===` 演算子（case equality）を使って判定します。これにより：
- クラス/モジュールでの型チェック
- 正規表現でのパターンマッチ
- Range での範囲チェック

などが可能になります。

#### 3. ブロックあり：カスタム条件

```ruby
[1, 2, 3, 4, 5].any? { |n| n > 3 }     # => true
[1, 2, 3].any? { |n| n.even? }         # => true (2 が偶数)
['apple', 'banana'].any? { |s| s.length > 6 }  # => false
```

ブロックを使うことで、任意の条件でフィルタリングできます。

### ユースケース

#### データの妥当性チェック

```ruby
# エラーが1つでもあるか確認
errors = [nil, nil, "Invalid email"]
has_errors = errors.any?  # => true

# 必須フィールドに値が入っているか確認
fields = [name, email, password]
all_filled = fields.any? { |field| field.nil? || field.empty? }
```

#### 配列の条件検索

```ruby
# 配列に特定の条件を満たす要素があるか
users = [
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 17 },
  { name: 'Charlie', age: 30 }
]

has_minors = users.any? { |user| user[:age] < 18 }  # => true
```

#### フィルタリングのショートカット

```ruby
# 不正な値が含まれていないかチェック
values = [100, 200, -50, 300]
has_negative = values.any? { |v| v < 0 }  # => true
```

### 注意点

#### 空配列は常に `false`

```ruby
[].any?              # => false
[].any? { true }     # => false
[].any?(Integer)     # => false
```

どんな条件でも、空配列に対しては `false` が返ります。

#### ショートサーキット評価（短絡評価）

`any?` は最初に条件を満たす要素を見つけた時点で評価を停止します：

```ruby
[1, 2, 3, 4, 5].any? do |n|
  puts "Checking #{n}"
  n > 2
end
# 出力:
# Checking 1
# Checking 2
# Checking 3
# => true (3 で条件を満たすため、4 と 5 はチェックされない)
```

これにより、大きな配列でもパフォーマンスが最適化されます。

#### ブロックと引数の両方を渡すとブロックは無視される

```ruby
[1, 2, 3].any?(Integer) { |n| n > 10 }  # => true
# ブロックは無視され、Integer チェックのみが実行される
```

### 関連メソッドとの比較

| メソッド | 条件 | 空配列の結果 |
|---------|------|--------------|
| `any?` | **いずれか1つでも**条件を満たす | `false` |
| `all?` | **すべて**が条件を満たす | `true` |
| `none?` | **すべて**が条件を満たさない | `true` |
| `one?` | **ちょうど1つだけ**が条件を満たす | `false` |

```ruby
arr = [1, 2, 3, 4, 5]

arr.any? { |n| n > 3 }   # => true (4, 5 が該当)
arr.all? { |n| n > 3 }   # => false (1, 2, 3 が該当しない)
arr.none? { |n| n > 10 } # => true (該当なし)
arr.one? { |n| n == 3 }  # => true (3 だけが該当)
```

### パフォーマンスの考慮事項

- **`include?` との違い**: 単純な値の存在チェックには `include?` の方が高速です
  ```ruby
  # 単純な値チェック
  [1, 2, 3].include?(2)      # より高速
  [1, 2, 3].any? { |n| n == 2 }  # やや低速

  # 条件チェック
  [1, 2, 3].any? { |n| n > 2 }   # any? が必要
  ```

- **`find` との使い分け**: 要素自体が必要な場合は `find` を使う
  ```ruby
  # 条件を満たす要素が必要
  result = [1, 2, 3].find { |n| n > 2 }  # => 3

  # 存在確認だけで十分
  result = [1, 2, 3].any? { |n| n > 2 }  # => true
  ```

### Enumerable モジュール

`any?` は `Enumerable` モジュールから提供されているため、配列以外でも使用できます：

```ruby
# Hash
{ a: 1, b: 2, c: 3 }.any? { |k, v| v > 2 }  # => true

# Range
(1..10).any? { |n| n > 5 }  # => true

# Set
require 'set'
Set[1, 2, 3].any?(&:even?)  # => true
```
