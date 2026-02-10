# Array#fill

- 日付: 2026-02-10
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-fill
- daily 記録: [daily/2026-02-10.md](../../daily/2026-02-10.md)

## 基本情報

### メソッドシグネチャ

```ruby
fill(object, start = nil, count = nil) → self
fill(object, range) → self
fill(start = nil, count = nil) {|index| ... } → self
fill(range) {|index| ... } → self
```

### 概要

選択された要素を `self` の中の要素と置き換えます。また、要素を `self` に追加することもあります。常に `self`（新しい配列ではない）を返します。

**重要**: `fill` は**破壊的メソッド**で、元の配列を直接変更します。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# fill(object, start = nil, count = nil) → self
# fill(object, range) → self
# fill(start = nil, count = nil) {|index| ... } → self
# fill(range) {|index| ... } → self

# Replaces selected elements in self; may add elements to self; always returns self (never a new array).
# 選択された要素を自身の中の要素と置き換えます。また、要素を自身に追加することもあります。常に自身（新しい配列ではない）を返します。

# In brief:
# 要約:

# Non-negative start.
['a', 'b', 'c', 'd'].fill('-', 1, 2)             # => ["a", "-", "-", "d"]
['a', 'b', 'c', 'd'].fill(1, 2) {|i| i.to_s }    # => ["a", "1", "2", "d"]

# Extends with specified values if necessary.
['a', 'b', 'c', 'd'].fill('-', 3, 2)             # => ["a", "b", "c", "-", "-"]
['a', 'b', 'c', 'd'].fill(3, 2) {|i| i.to_s }    # => ["a", "b", "c", "3", "4"]

# Fills with nils if necessary.
['a', 'b', 'c', 'd'].fill('-', 6, 2)             # => ["a", "b", "c", "d", nil, nil, "-", "-"]
['a', 'b', 'c', 'd'].fill(6, 2) {|i| i.to_s }    # => ["a", "b", "c", "d", nil, nil, "6", "7"]

# For negative start, counts backwards from the end.
['a', 'b', 'c', 'd'].fill('-', -3, 3)            # => ["a", "-", "-", "-"]
['a', 'b', 'c', 'd'].fill(-3, 3) {|i| i.to_s }   # => ["a", "1", "2", "3"]

# Range.
['a', 'b', 'c', 'd'].fill('-', 1..2)             # => ["a", "-", "-", "d"]
['a', 'b', 'c', 'd'].fill(1..2) {|i| i.to_s }    # => ["a", "1", "2", "d"]
```

## 深掘り・補足

### 重要なポイント

#### 1. 破壊的メソッドである

- `fill` は元の配列を直接変更します（`!` はついていませんが破壊的です）
- 必ず `self` を返します（新しい配列は作成されません）
- メソッドチェーンで使用可能

```ruby
array = ['a', 'b', 'c', 'd']
array.fill('-', 1, 2).reverse  # => ["d", "-", "-", "a"]
array  # => ["a", "-", "-", "d"] (元の配列も変更されている)
```

#### 2. 配列を自動的に拡張する

- 範囲外のインデックスを指定すると、配列が自動的に拡張されます
- 必要に応じて `nil` で埋められます

```ruby
['a', 'b'].fill('-', 5, 2)  # => ["a", "b", nil, nil, nil, "-", "-"]
```

### 主な使用パターン

#### パターン 1: オブジェクトで埋める

```ruby
# 範囲を指定して同じオブジェクトで埋める
array = [1, 2, 3, 4, 5]
array.fill(0, 1, 3)  # => [1, 0, 0, 0, 5]

# 範囲指定で Range を使う
array = [1, 2, 3, 4, 5]
array.fill(0, 1..3)  # => [1, 0, 0, 0, 5]

# 全体を埋める (start と count を省略)
array = [1, 2, 3, 4, 5]
array.fill(0)  # => [0, 0, 0, 0, 0]
```

#### パターン 2: ブロックで埋める

```ruby
# インデックスに基づいて値を生成
array = ['a', 'b', 'c', 'd']
array.fill(1, 2) { |i| i * 10 }  # => ["a", 10, 20, "d"]

# 配列全体を連番で初期化
array = Array.new(5)
array.fill { |i| i + 1 }  # => [1, 2, 3, 4, 5]
```

#### パターン 3: 負のインデックス

```ruby
# 末尾から数えて置き換える
array = ['a', 'b', 'c', 'd']
array.fill('-', -2, 2)  # => ["a", "b", "-", "-"]
```

#### パターン 4: Range での柔軟な指定

```ruby
# 終端を含まない範囲 (exclusive range)
['a', 'b', 'c', 'd'].fill('-', 1...3)  # => ["a", "-", "-", "d"]

# 終端なし範囲 (endless range)
['a', 'b', 'c', 'd'].fill('-', 2..)  # => ["a", "b", "-", "-"]

# 始端なし範囲 (beginless range)
['a', 'b', 'c', 'd'].fill('-', ..2)  # => ["-", "-", "-", "d"]
```

### 実践的なユースケース

#### 1. 配列の初期化

```ruby
# 特定のサイズの配列を同じ値で初期化
array = Array.new(10).fill(0)  # => [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

# インデックスベースで初期化
array = Array.new(5).fill { |i| i * 2 }  # => [0, 2, 4, 6, 8]
```

#### 2. データのリセット

```ruby
# 一部の要素をデフォルト値にリセット
scores = [100, 85, 92, 78, 88]
scores.fill(0, 2, 2)  # => [100, 85, 0, 0, 88]
```

#### 3. テンプレート配列の作成

```ruby
# フォームのデフォルト値を設定
form_fields = Array.new(5).fill('')
```

### 注意点や落とし穴

#### ⚠️ オブジェクト参照の共有

**重大な注意点**: オブジェクトで埋める場合、すべての要素が**同じオブジェクトの参照**を共有します。

```ruby
# 危険な例：すべて同じ配列オブジェクトを参照
matrix = Array.new(3).fill([])
matrix[0] << 1
matrix  # => [[1], [1], [1]]  # すべての行が変更される！

# 正しい例：ブロックで毎回新しいオブジェクトを作成
matrix = Array.new(3).fill { [] }
matrix[0] << 1
matrix  # => [[1], [], []]  # 期待通りの動作
```

**安全なオブジェクト**（immutable objects）：

- シンボル (`:symbol`)
- 数値 (`1`, `3.14`)
- `nil`
- `true` / `false`

#### ⚠️ count が 0 または負の場合

```ruby
# 何も変更されない
['a', 'b', 'c'].fill('-', 1, 0)     # => ["a", "b", "c"]
['a', 'b', 'c'].fill('-', 1, -1)    # => ["a", "b", "c"]
```

#### ⚠️ 範囲外のインデックス

```ruby
# 配列が自動的に拡張され、nil で埋められる
['a', 'b'].fill('-', 5, 2)  # => ["a", "b", nil, nil, nil, "-", "-"]

# 負のインデックスが範囲外の場合、先頭から開始
['a', 'b', 'c'].fill('-', -10, 2)  # => ["-", "-", "c"]
```

### パフォーマンスの考慮事項

#### メリット

- **メモリ効率が良い**: 既存の配列を直接変更するため、新しいメモリ割り当てが不要
- **高速**: インプレース操作のため、配列のコピーが発生しない

#### 適用シーン

- 大きな配列の初期化
- データのリセット操作
- バッファの準備

```ruby
# 効率的：既存の配列を再利用
buffer = Array.new(1000)
buffer.fill(0)  # 高速

# 非効率：毎回新しい配列を作成
buffer = Array.new(1000, 0)  # これも効率的だが、再利用の場合は fill が有利
```

### 関連メソッドとの比較

#### `Array.new` との違い

```ruby
# Array.new：新しい配列を作成
Array.new(5, 0)  # => [0, 0, 0, 0, 0]

# fill：既存の配列を変更
array = [1, 2, 3, 4, 5]
array.fill(0)    # => [0, 0, 0, 0, 0]
```

#### `map` / `map!` との違い

```ruby
# map!：既存の要素に基づいて変換
[1, 2, 3].map! { |x| x * 2 }  # => [2, 4, 6]

# fill：インデックスに基づいて生成
[1, 2, 3].fill { |i| i * 2 }  # => [0, 2, 4]
```

### まとめ

`Array#fill` は配列の要素を効率的に置き換えるメソッドです。

**使いどころ**：

- 配列の初期化
- 一部の要素をリセット
- インデックスベースの値生成

**注意点**：

- 破壊的メソッドである
- オブジェクト参照の共有に注意
- 配列が自動的に拡張される
