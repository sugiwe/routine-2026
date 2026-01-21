# Array#combination

- 日付: 2026-01-21
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-combination

## 基本情報

```ruby
combination(count) {|element| ... } → self
combination(count) → new_enumerator
```

配列の要素から指定したサイズ `count` の組み合わせ（combination）を生成するメソッドです。

## daily からの記録

### コード例・補足

```ruby
# When a block and a positive integer-convertible object argument count (0 < count <= self.size) are given, calls the block with each combination of self of size count; returns self:
# ブロックと、正の整数で変換可能なオブジェクト（0 < count <= self.size）を引数として与えられた場合、sizeがcountのselfの各組み合わせでブロックを呼び出し、selfを返します：

a = %w[a b c]                                   # => ["a", "b", "c"]
a.combination(2) {|combination| p combination } # => ["a", "b", "c"]
# Output:
["a", "b"]
["a", "c"]
["b", "c"]

# The order of the yielded combinations is not guaranteed.
# 生成される組み合わせの順序は保証されません。

# When count is zero, calls the block once with a new empty array:
# countがゼロの場合、新しい空の配列でブロックを一度呼び出します:
a.combination(0) {|combination| p combination }  # => ["a", "b", "c"]
[].combination(0) {|combination| p combination } # => []
# Output:
[]
[]

# When count is negative or larger than self.size and self is non-empty, does not call the block:
# countが負の場合、またはself.sizeより大きい場合（かつselfが空でない場合）、ブロックは呼び出されません。
a.combination(-1) {|combination| fail 'Cannot happen' } # => ["a", "b", "c"]
a.combination(4)  {|combination| fail 'Cannot happen' } # => ["a", "b", "c"]

# With no block given, returns a new Enumerator.
```

## 深掘り・補足

### 重要なポイント

#### 1. 組み合わせ（Combination）とは

数学的な **組み合わせ (nCr)** を生成します。順列（permutation）とは異なり、**順序は考慮されません**。

- 組み合わせ: `["a", "b"]` と `["b", "a"]` は同じ
- 順列: `["a", "b"]` と `["b", "a"]` は別

#### 2. 引数 `count` の挙動

| count の値 | 挙動 |
|-----------|------|
| `0` | 空配列 `[]` を 1 回 yield する |
| `1 <= count <= self.size` | 指定サイズの組み合わせを生成 |
| `count < 0` | ブロックを呼ばない（何も生成しない） |
| `count > self.size` | ブロックを呼ばない（何も生成しない） |

#### 3. 戻り値

- **ブロックあり**: `self` を返す（元の配列）
- **ブロックなし**: `Enumerator` を返す

#### 4. 順序の保証なし

⚠️ **重要**: 生成される組み合わせの順序は保証されません。順序に依存するコードは避けましょう。

### 実践的なユースケース

#### 1. すべての組み合わせを配列で取得

```ruby
words = ["Ruby", "Python", "JavaScript"]
pairs = words.combination(2).to_a
# => [["Ruby", "Python"], ["Ruby", "JavaScript"], ["Python", "JavaScript"]]
```

#### 2. 条件に合う組み合わせを検索

```ruby
numbers = [1, 2, 3, 4, 5]
# 合計が 7 になるペアを探す
numbers.combination(2).select { |a, b| a + b == 7 }
# => [[2, 5], [3, 4]]
```

#### 3. チーム分けやマッチング

```ruby
players = ["Alice", "Bob", "Carol", "Dave"]
# 2人1組のチームを全パターン生成
teams = players.combination(2).to_a
# => [["Alice", "Bob"], ["Alice", "Carol"], ["Alice", "Dave"],
#     ["Bob", "Carol"], ["Bob", "Dave"], ["Carol", "Dave"]]
```

#### 4. Enumerator として使う

```ruby
# 遅延評価で大きな配列も効率的に処理
big_array = (1..100).to_a
big_array.combination(3).lazy.first(5).to_a
# => 最初の5つの組み合わせだけ生成
```

### 注意点

#### 1. 計算量

組み合わせの数は **nCr = n! / (r! * (n-r)!)** で計算されます。大きな配列では膨大な数になります：

```ruby
(1..20).to_a.combination(10).size
# => 184,756 通り！
```

大きな配列では `lazy` を使うか、必要な分だけ取得しましょう。

#### 2. ブロックなしの場合

ブロックを省略すると `Enumerator` が返るため、`each`, `map`, `select` などのメソッドチェーンが可能です：

```ruby
[1, 2, 3, 4].combination(2).map { |a, b| a * b }
# => [2, 3, 4, 6, 8, 12]
```

### 関連メソッド

- `Array#permutation`: 順列を生成（順序を考慮）
- `Array#repeated_combination`: 重複を許す組み合わせ
- `Array#repeated_permutation`: 重複を許す順列
- `Array#product`: 直積（デカルト積）を生成

### パフォーマンスの考慮事項

大きな配列で `combination` を使う場合：

1. **遅延評価を活用**: `lazy` を使って必要な分だけ処理
2. **サイズを確認**: `combination(n).size` で事前に件数をチェック
3. **早期終了**: `find` や `any?` で条件に合う組み合わせが見つかったら即終了

```ruby
# 悪い例: すべて生成してから検索
[1, 2, 3, 4, 5, 6].combination(3).to_a.find { |combo| combo.sum == 10 }

# 良い例: 見つかったら即終了
[1, 2, 3, 4, 5, 6].combination(3).find { |combo| combo.sum == 10 }
```
