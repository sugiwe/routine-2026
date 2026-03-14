# Array#repeated_combination

- 日付: 2026-03-14
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-repeated_combination
- daily 記録: [daily/2026-03-14.md](../../daily/2026-03-14.md)

## 基本情報

```ruby
repeated_combination(size) { |combination| ... } → self
repeated_combination(size) → Enumerator
```

**重複を許可した組み合わせ**を生成するメソッドです。`self` の要素から長さ `size` の組み合わせを作りますが、各要素を**複数回使用できる**点が特徴です。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

初めて見るメソッドだ。

### コード例

```ruby
# With a block given, calls the block with each repeated combination of length size of the elements of self; each combination is an array; returns self. The order of the combinations is indeterminate.
# ブロックが与えられた場合、自身の要素から長さ`size`の重複組み合わせをそれぞれブロックを呼び出して渡します。各組み合わせは配列であり、自身を返します。組み合わせの順序は不定です。
# If a positive integer argument size is given, calls the block with each size-tuple repeated combination of the elements of self. For an array of 3 elements, the number of combinations is (size+1)(size+2)/2.
# 正の整数引数 `size` が与えられた場合、`self` の要素からなる `size` 個のタプルの重複組み合わせそれぞれについてブロックを呼び出します。要素数が3の場合、組み合わせの総数は (size+1)(size+2)/2 です。

# Examples:
# size is 1:
c = []
[0, 1, 2].repeated_combination(1) {|combination| c.push(combination) }
c # => [[0], [1], [2]]

# size is 2:
c = []
[0, 1, 2].repeated_combination(2) {|combination| c.push(combination) }
c # => [[0, 0], [0, 1], [0, 2], [1, 1], [1, 2], [2, 2]]

# If size is zero, calls the block once with an empty array.
# サイズがゼロの場合、空の配列でブロックを一度呼び出します。
# If size is negative, does not call the block:
# サイズが負の場合、ブロックは呼び出されません。
[0, 1, 2].repeated_combination(-1) {|combination| fail 'Cannot happen' }

# With no block given, returns a new Enumerator.
# Related: see Methods for Combining.
```

## 深掘り・補足

### `combination` との違い

**`combination`（通常の組み合わせ）**：
```ruby
[0, 1, 2].combination(2).to_a
# => [[0, 1], [0, 2], [1, 2]]
# 各要素は1回のみ使用
```

**`repeated_combination`（重複を許可した組み合わせ）**：
```ruby
[0, 1, 2].repeated_combination(2).to_a
# => [[0, 0], [0, 1], [0, 2], [1, 1], [1, 2], [2, 2]]
# 同じ要素を複数回使用できる
```

### 重要なポイント

1. **同じ要素を複数回使える**
   - `[0, 0]`, `[1, 1]`, `[2, 2]` のような組み合わせも生成される
   - これが `combination` との最大の違い

2. **組み合わせの総数**
   - 配列の要素数を `n`、サイズを `r` とすると
   - 通常の combination: `nCr = n! / (r! * (n-r)!)`
   - repeated_combination: `nHr = (n+r-1)Cr`
   - 例: `n=3, r=2` の場合
     - combination: 3通り
     - repeated_combination: 6通り

3. **順序は不定**
   - 生成される順序は保証されない
   - 順序に依存したコードは書かない

### 実用的なユースケース

1. **重複を許可した選択肢の組み合わせ**
   ```ruby
   # トッピングを2つ選ぶ（同じものを2つ選んでもOK）
   toppings = ['チーズ', 'ベーコン', 'マッシュルーム']
   toppings.repeated_combination(2).to_a
   # => [["チーズ", "チーズ"], ["チーズ", "ベーコン"], ...]
   ```

2. **数学的な組み合わせ問題**
   ```ruby
   # サイコロを2回振った時の目の組み合わせ（順序を区別しない）
   [1, 2, 3, 4, 5, 6].repeated_combination(2).count
   # => 21通り
   ```

3. **在庫から複数個取り出すパターン**
   ```ruby
   # 3種類の商品から2個購入するパターン
   products = ['A', 'B', 'C']
   products.repeated_combination(2).each do |combo|
     puts "購入: #{combo.join(', ')}"
   end
   # 購入: A, A
   # 購入: A, B
   # 購入: A, C
   # 購入: B, B
   # 購入: B, C
   # 購入: C, C
   ```

### 特殊なケース

1. **size が 0 の場合**
   ```ruby
   [1, 2, 3].repeated_combination(0).to_a
   # => [[]]  # 空の配列を1つ返す
   ```

2. **size が負の場合**
   ```ruby
   [1, 2, 3].repeated_combination(-1).to_a
   # => []  # 空の配列を返す（組み合わせなし）
   ```

3. **配列が空の場合**
   ```ruby
   [].repeated_combination(2).to_a
   # => []  # 要素がないので組み合わせも作れない
   ```

### パフォーマンスの考慮事項

- 組み合わせの数は急速に増加する
  - `n=10, r=5` の場合: 2,002通り
  - `n=20, r=5` の場合: 42,504通り
- 大きな配列や大きなサイズでは、メモリ使用量に注意
- 可能な限り遅延評価（Enumerator）を活用する

```ruby
# 悪い例：すべてを配列に展開
all_combos = large_array.repeated_combination(5).to_a  # メモリを大量消費

# 良い例：必要なものだけ処理
large_array.repeated_combination(5).each do |combo|
  break if some_condition  # 条件を満たしたら早期終了
  process(combo)
end
```

### まとめ

- `repeated_combination` は**重複を許可した組み合わせ**を生成
- 通常の `combination` では作れない `[1, 1]` のような組み合わせも作れる
- 数学的問題や実世界の「同じものを複数選べる」シナリオで有用
- 組み合わせの数が急増するので、パフォーマンスに注意
