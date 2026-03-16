# Array#repeated_permutation

- 日付: 2026-03-15
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-repeated_permutation
- daily 記録: [daily/2026-03-15.md](../../daily/2026-03-15.md)

## 基本情報

```ruby
repeated_permutation(size) { |permutation| ... } → self
repeated_permutation(size) → Enumerator
```

自身の要素から長さ `size` の重複順列（repeated permutation）を生成するメソッド。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

`repeated_combination`は重複無しっていうか `[0,2]`と`[2,0]`を同じものとして見てたけど、こっちは別物として見るってことか

### コード例

```ruby
# With a block given, calls the block with each repeated permutation of length size of the elements of self; each permutation is an array; returns self. The order of the permutations is indeterminate.
# ブロックが与えられた場合、自身の要素から長さ `size` の重複順列を生成し、それぞれの順列（配列として）を引数としてブロックを呼び出します。自身を返します。順列の順序は不定です。
# If a positive integer argument size is given, calls the block with each size-tuple repeated permutation of the elements of self. The number of permutations is self.size**size.
# 正の整数引数 `size` が与えられた場合、`self` の要素から `size` 個の組を作り、重複を許した順列ごとにブロックを呼び出します。順列の総数は `self.size ** size` となります。

# Examples:
# - size is 1:
p = []
[0, 1, 2].repeated_permutation(1) {|permutation| p.push(permutation) }
p # => [[0], [1], [2]]

# - size is 2:
p = []
[0, 1, 2].repeated_permutation(2) {|permutation| p.push(permutation) }
p # => [[0, 0], [0, 1], [0, 2], [1, 0], [1, 1], [1, 2], [2, 0], [2, 1], [2, 2]]

# If size is zero, calls the block once with an empty array.
# If size is negative, does not call the block:

[0, 1, 2].repeated_permutation(-1) {|permutation| fail 'Cannot happen' }

With no block given, returns a new Enumerator.
Related: see Methods for Combining.
```

## 深掘り・補足

### repeated_combination との違い

ユーザーの第一印象は正確です！重要な違いは：

| メソッド | `[0, 2]` と `[2, 0]` | 考え方 |
|---------|---------------------|--------|
| `repeated_combination` | 同じもの（順序を区別しない） | 組み合わせ（combination） |
| `repeated_permutation` | 別のもの（順序を区別する） | 順列（permutation） |

### 順列の総数

配列の要素数を `n`、生成する順列の長さを `size` とすると：

- **順列の総数** = `n ** size`

例：`[0, 1, 2]` で `size = 2` の場合
- 総数 = 3² = 9 通り
- 実際：`[[0, 0], [0, 1], [0, 2], [1, 0], [1, 1], [1, 2], [2, 0], [2, 1], [2, 2]]`

### 実践的なユースケース

1. **パスワード生成の候補列挙**

```ruby
# 数字2桁のすべての組み合わせを生成
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9].repeated_permutation(2).to_a
# => [[0, 0], [0, 1], ..., [9, 9]] （100通り）
```

2. **テストケースの網羅**

```ruby
# ON/OFF の3つのスイッチのすべての状態を生成
[:on, :off].repeated_permutation(3).each do |state|
  test_configuration(state)
end
# => 2³ = 8通りのテストケース
```

3. **カードゲームのドロー（同じカードを引ける場合）**

```ruby
# 1〜5のカードから3枚引く（引いたカードは戻す）
(1..5).to_a.repeated_permutation(3).to_a.size
# => 5³ = 125通り
```

### パフォーマンスの考慮事項

順列の数は **指数関数的** に増加します：

```ruby
# 要素数10、size = 5 の場合
[*0..9].repeated_permutation(5).to_a.size
# => 10⁵ = 100,000 通り

# 要素数10、size = 10 の場合
[*0..9].repeated_permutation(10).to_a.size
# => 10¹⁰ = 10,000,000,000 通り（100億！）
```

**注意点：**
- `size` が大きくなると、メモリ消費とCPU負荷が急激に増加
- 全パターンを配列化せず、ブロックで逐次処理するのが推奨

### 関連メソッドとの比較

| メソッド | 重複 | 順序 | 総数（要素数n、長さsize） |
|---------|------|------|---------------------------|
| `combination` | ❌ | 区別しない | `C(n, size)` = n! / (size! × (n-size)!) |
| `permutation` | ❌ | 区別する | `P(n, size)` = n! / (n-size)! |
| `repeated_combination` | ✅ | 区別しない | `H(n, size)` = C(n+size-1, size) |
| `repeated_permutation` | ✅ | 区別する | `n ** size` |

### エッジケース

```ruby
# size = 0 の場合：空配列を1回だけ yield
[1, 2, 3].repeated_permutation(0).to_a
# => [[]]

# size が負の場合：何も yield しない
[1, 2, 3].repeated_permutation(-1).to_a
# => []

# ブロックなしの場合：Enumerator を返す
enum = [1, 2].repeated_permutation(2)
enum.class # => Enumerator
enum.first(3) # => [[1, 1], [1, 2], [2, 1]]
```

### まとめ

- **順序が重要** なら `permutation` 系
- **重複を許す** なら `repeated_` 系
- 両方満たすのが `repeated_permutation`

ユーザーが気づいたように、combination と permutation の最大の違いは「順序を区別するかどうか」です！
