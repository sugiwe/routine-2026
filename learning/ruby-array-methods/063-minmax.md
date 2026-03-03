# Array#minmax

- 日付: 2026-03-04
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-minmax
- daily 記録: [daily/2026-03-04.md](../../daily/2026-03-04.md)

## 基本情報

```ruby
minmax → [min_val, max_val]
minmax {|a, b| ... } → [min_val, max_val]
```

自身の要素から最小値と最大値を持つ要素をそれぞれ抽出し、2要素の配列として返します。自身は変更されません。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

たまに競プロで見かけた気がする。実際どういう時に使うのかな？

### コード例

```ruby
# Returns a 2-element array containing the minimum-valued and maximum-valued elements from self; does not modify self.
# 自身の要素から最小値と最大値を持つ要素をそれぞれ抽出し、2要素の配列として返します。自身は変更されません。

# With no block given, the minimum and maximum values are determined using method <=>:
# ブロックが与えられない場合、最小値と最大値は`<=>`メソッドを用いて決定されます。
[1, 0, 3, 2].minmax # => [0, 3]

# With a block given, the block must return a numeric; the block is called `self.size - 1` times to compare elements; returns the elements having the minimum and maximum values per the block:
# ブロックが与えられた場合、そのブロックは数値を返す必要があります。このブロックは要素を比較するために `self.size - 1` 回呼び出され、ブロックに基づいて最小値と最大値を持つ要素を返します。
['0', '', '000', '00'].minmax {|a, b| a.size <=> b.size }
# => ["", "000"]
```

## 深掘り・補足

### minmax の利点

**1. パフォーマンス効率**

`min` と `max` を別々に呼ぶ代わりに `minmax` を使うと、配列を1回だけ走査すれば済みます：

```ruby
# 効率が悪い方法（配列を2回走査）
array = [5, 2, 8, 1, 9, 3]
min_val = array.min  # 1回目の走査
max_val = array.max  # 2回目の走査

# 効率が良い方法（配列を1回だけ走査）
min_val, max_val = array.minmax  # 1回の走査で両方取得
```

大きな配列では、この差が顕著になります。

**2. コードの簡潔性**

```ruby
# before
scores = [75, 82, 91, 68, 95]
min_score = scores.min
max_score = scores.max

# after
min_score, max_score = scores.minmax
```

### 実践的なユースケース

**1. データの範囲を把握する**

```ruby
temperatures = [15, 22, 18, 25, 12, 20]
min_temp, max_temp = temperatures.minmax
puts "今日の気温: #{min_temp}°C 〜 #{max_temp}°C"
# => "今日の気温: 12°C 〜 25°C"
```

**2. スコアリングやレンジ計算**

```ruby
scores = [85, 92, 78, 95, 88]
min, max = scores.minmax
range = max - min
puts "スコアの範囲: #{range}点"
# => "スコアの範囲: 17点"
```

**3. 正規化（Normalization）**

```ruby
values = [10, 50, 30, 80, 20]
min, max = values.minmax

normalized = values.map do |v|
  (v - min).to_f / (max - min)
end
# => [0.0, 0.5714285714285714, 0.2857142857142857, 1.0, 0.14285714285714285]
```

### ブロックを使った柔軟な比較

**文字列の長さで比較:**

```ruby
words = ['a', 'abc', 'ab', 'abcd']
shortest, longest = words.minmax {|a, b| a.size <=> b.size }
# => ["a", "abcd"]
```

**カスタムオブジェクトの比較:**

```ruby
Person = Struct.new(:name, :age)
people = [
  Person.new('Alice', 30),
  Person.new('Bob', 25),
  Person.new('Carol', 35)
]

youngest, oldest = people.minmax {|a, b| a.age <=> b.age }
# youngest.name => "Bob" (25歳)
# oldest.name => "Carol" (35歳)
```

### 競技プログラミングでの活用

ユーザーのメモにあるように、競プロでよく見かける理由：

```ruby
# 配列の最小値と最大値の差を求める問題
n = gets.to_i
arr = gets.split.map(&:to_i)
min, max = arr.minmax
puts max - min
```

**AtCoder などでの典型問題:**
- 配列の要素の範囲を求める
- 最小値と最大値を使って何か計算する
- 正規化やスケーリング

### 注意点

**1. 空の配列では nil を返す**

```ruby
[].minmax  # => [nil, nil]
```

**2. すべて同じ値の場合**

```ruby
[5, 5, 5].minmax  # => [5, 5]
```

**3. 比較不可能な要素がある場合はエラー**

```ruby
[1, 'a', 2].minmax  # ArgumentError: comparison of Integer with String failed
```

### 関連メソッド

- `min`: 最小値のみ取得
- `max`: 最大値のみ取得
- `minmax_by`: ブロックの戻り値で比較（より読みやすい）

```ruby
words = ['a', 'abc', 'ab', 'abcd']

# minmax with block
words.minmax {|a, b| a.size <=> b.size }

# minmax_by (より読みやすい)
words.minmax_by {|word| word.size }
# 両方とも => ["a", "abcd"]
```

### まとめ

`minmax` は、最小値と最大値を同時に取得したい場合に非常に便利なメソッドです。特に：

- **パフォーマンス**: 配列を1回だけ走査
- **簡潔性**: コードが短く読みやすい
- **実用性**: データ分析、正規化、範囲計算など幅広く使える

競技プログラミングでは頻出パターンですが、実務でもデータの範囲を把握したり、統計処理を行う際に役立ちます。
