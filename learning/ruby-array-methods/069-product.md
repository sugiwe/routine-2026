# Array#product

- 日付: 2026-03-10
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-product
- daily 記録: [daily/2026-03-10.md](../../daily/2026-03-10.md)

## 基本情報

### メソッドシグネチャ

```ruby
product(*other_arrays) → array
product(*other_arrays) { |combination| ... } → self
```

### 概要

自身の配列と `other_arrays` の要素のすべての組み合わせ（デカルト積）を計算します。組み合わせの数は、すべての配列のサイズの積になります。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

「返される組み合わせの順序は不定」←そうなんだ、順番しっかり定められてると思ってた👀

### コード例

```ruby
# Computes all combinations of elements from all the arrays, including both self and other_arrays:
# 自身と`other_arrays`の両方を含む、すべての配列から要素の組み合わせをすべて計算します。
# The number of combinations is the product of the sizes of all the arrays, including both self and other_arrays.
# 組み合わせの数は、自身とother_arraysの両方を含む、すべての配列のサイズの積です。
# The order of the returned combinations is indeterminate.
# 返される組み合わせの順序は不定です。
# With no block given, returns the combinations as an array of arrays:
# ブロックが与えられない場合、組み合わせの配列を返します。
p = [0, 1].product([2, 3])
# => [[0, 2], [0, 3], [1, 2], [1, 3]]
p.size # => 4
p = [0, 1].product([2, 3], [4, 5])
# => [[0, 2, 4], [0, 2, 5], [0, 3, 4], [0, 3, 5], [1, 2, 4], [1, 2, 5], [1, 3, 4], [1, 3,...
p.size # => 8

# If self or any argument is empty, returns an empty array:
# もし自己または引数のいずれかが空であれば、空の配列を返します。
[].product([2, 3], [4, 5]) # => []
[0, 1].product([2, 3], []) # => []

# If no argument is given, returns an array of 1-element arrays, each containing an element of self:
# 引数が指定されていない場合、selfの各要素をそれぞれ格納した1要素の配列を複数含む配列を返します。
a = [0, 1, 2]
a.product # => [[0], [1], [2]]

# With a block given, calls the block with each combination; returns self:
# ブロックが与えられた場合、ブロックを各組み合わせで呼び出し、selfを返します。
p = []
[0, 1].product([2, 3]) {|combination| p.push(combination) }
p # => [[0, 2], [0, 3], [1, 2], [1, 3]]

# If self or any argument is empty, does not call the block:
# もし自身、または引数のいずれかが空の場合は、ブロックを呼び出しません。
[].product([2, 3], [4, 5]) {|combination| fail 'Cannot happen' }
# => []
[0, 1].product([2, 3], []) {|combination| fail 'Cannot happen' }
# => [0, 1]

# If no argument is given, calls the block with each element of self as a 1-element array:
# 引数が与えられない場合、各要素を1要素の配列としてブロックを呼び出します。
p = []
[0, 1].product {|combination| p.push(combination) }
p # => [[0], [1]]

# Related: see Methods for Combining.
```

## 深掘り・補足

### 重要なポイント

#### 1. デカルト積（Cartesian Product）

`product` は数学的なデカルト積を計算するメソッドです。複数の集合から 1 つずつ要素を取り出したすべての組み合わせを生成します。

```ruby
colors = [:red, :blue]
sizes = [:S, :M, :L]

# すべての色とサイズの組み合わせ
combinations = colors.product(sizes)
# => [[:red, :S], [:red, :M], [:red, :L], [:blue, :S], [:blue, :M], [:blue, :L]]
```

#### 2. 順序の不定性について

ドキュメントには「The order of the returned combinations is indeterminate（返される組み合わせの順序は不定）」と記載されていますが、**実際には現在の Ruby 実装では一定の順序で返されます**。

しかし、この順序に依存したコードは書くべきではありません。将来の Ruby のバージョンで順序が変わる可能性があるためです。

**順序に依存しない使い方：**
```ruby
# ✅ 良い例: 順序に依存しない
result = [1, 2].product([3, 4])
result.include?([1, 3]) # => true

# ❌ 悪い例: 順序に依存
result = [1, 2].product([3, 4])
result[0] == [1, 3] # => たまたま true だが、依存すべきでない
```

#### 3. 組み合わせの数の計算

組み合わせの総数は、すべての配列のサイズの**積**（product）になります。

```ruby
[1, 2].product([3, 4, 5], [6, 7])
# サイズ: 2 × 3 × 2 = 12 個の組み合わせ
# => [[1, 3, 6], [1, 3, 7], [1, 4, 6], [1, 4, 7], [1, 5, 6], [1, 5, 7],
#     [2, 3, 6], [2, 3, 7], [2, 4, 6], [2, 4, 7], [2, 5, 6], [2, 5, 7]]
```

#### 4. 空配列の扱い

いずれかの配列が空の場合、結果も空配列になります。これは数学的に正しい挙動です（空集合とのデカルト積は空集合）。

```ruby
[1, 2].product([])       # => []
[].product([1, 2], [3])  # => []
```

### ユースケース

#### 1. 商品バリエーションの生成

```ruby
colors = ['Red', 'Blue', 'Green']
sizes = ['S', 'M', 'L']
materials = ['Cotton', 'Polyester']

# すべてのバリエーションを生成
variants = colors.product(sizes, materials)
variants.each do |color, size, material|
  puts "#{color} / #{size} / #{material}"
end
# => Red / S / Cotton
#    Red / S / Polyester
#    Red / M / Cotton
#    ...（計 18 通り）
```

#### 2. グリッド座標の生成

```ruby
x_coords = [0, 1, 2]
y_coords = [0, 1, 2]

grid = x_coords.product(y_coords)
# => [[0, 0], [0, 1], [0, 2], [1, 0], [1, 1], [1, 2], [2, 0], [2, 1], [2, 2]]
```

#### 3. テストケースの生成

```ruby
browsers = ['Chrome', 'Firefox', 'Safari']
os = ['Windows', 'macOS', 'Linux']

# すべてのブラウザ × OS の組み合わせでテスト
test_cases = browsers.product(os)
test_cases.each do |browser, operating_system|
  run_test(browser: browser, os: operating_system)
end
```

### パフォーマンスの考慮事項

#### メモリ使用量

`product` はすべての組み合わせを生成するため、配列が大きい場合は**メモリ使用量が爆発的に増加**します。

```ruby
# 要注意: 100 × 100 × 100 = 1,000,000 個の配列を生成
result = (1..100).to_a.product((1..100).to_a, (1..100).to_a)
```

**対策: ブロック付きで呼び出す**

ブロック付きで呼び出すと、すべての組み合わせを一度にメモリに保持せず、1 つずつ処理できます。

```ruby
# ✅ メモリ効率的
(1..100).to_a.product((1..100).to_a, (1..100).to_a) do |a, b, c|
  # 各組み合わせを 1 つずつ処理
  process(a, b, c)
end
```

### 関連メソッドとの比較

#### `Array#permutation` との違い

- **`product`**: 複数の配列から 1 つずつ要素を取る（デカルト積）
- **`permutation`**: 同じ配列から重複なしで複数要素を取る（順列）

```ruby
# product: 複数の配列から
[1, 2].product([3, 4])
# => [[1, 3], [1, 4], [2, 3], [2, 4]]

# permutation: 同じ配列から（重複なし）
[1, 2, 3].permutation(2).to_a
# => [[1, 2], [1, 3], [2, 1], [2, 3], [3, 1], [3, 2]]
```

#### `Array#repeated_permutation` との違い

- **`product`**: 複数の**異なる**配列から要素を取る
- **`repeated_permutation`**: 同じ配列から重複を許して要素を取る（重複順列）

```ruby
# product: 異なる配列から
[1, 2].product([1, 2])
# => [[1, 1], [1, 2], [2, 1], [2, 2]]

# repeated_permutation: 同じ配列から（重複あり）
[1, 2].repeated_permutation(2).to_a
# => [[1, 1], [1, 2], [2, 1], [2, 2]]

# 結果は同じだが、意味が異なる！
```

### 注意点

1. **順序に依存しない**: 将来の実装変更に備える
2. **メモリ使用量**: 大きな配列では組み合わせ数が爆発的に増加
3. **ブロック付きを活用**: メモリ効率を改善できる
4. **空配列のチェック**: 意図しない空結果を避けるため事前確認

### まとめ

`Array#product` は、複数の配列からすべての組み合わせを生成する強力なメソッドです。商品バリエーション、テストケース、グリッド座標など、実務でも頻繁に使われます。ただし、大規模データではメモリ使用量に注意し、ブロック付きの呼び出しを検討しましょう。
