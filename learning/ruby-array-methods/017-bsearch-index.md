# Array#bsearch_index

- 日付: 2026-01-17
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-bsearch_index

## 基本情報

```ruby
bsearch_index {|element| ... } → integer or nil
bsearch_index → new_enumerator
```

バイナリサーチで見つかった要素の整数インデックスを返します。見つからなかった場合は nil を返します。

## daily からの記録

### メモ

（記載なし）

### コード例・補足

```ruby
# Returns the integer index of the element from self found by a binary search, or nil if the search found no suitable element.
# バイナリサーチで見つかった要素の整数インデックスを返します。見つからなかった場合は nil を返します。

# See Binary Searching.

# Related: see Methods for Fetching.
```

## 深掘り・補足

### メソッドの概要

`bsearch_index` は、ソート済み配列に対してバイナリサーチ（二分探索）を実行し、条件に合致する要素の**インデックス**を返すメソッドです。

### bsearch との違い

- `bsearch`: 条件に合致する**要素そのもの**を返す
- `bsearch_index`: 条件に合致する要素の**インデックス**を返す

インデックスが必要な場合は `bsearch_index` を使うことで、後から `array.index` を呼ぶ必要がなくなります。

### 使用例

```ruby
# ソート済み配列から、10以上の最小要素のインデックスを取得
array = [1, 3, 5, 7, 9, 11, 13, 15]
index = array.bsearch_index { |x| x >= 10 }
# => 5 (要素 11 のインデックス)

# 要素が見つからない場合
array = [1, 3, 5, 7, 9]
index = array.bsearch_index { |x| x >= 20 }
# => nil
```

### 2つの探索モード

バイナリサーチには2つのモードがあります：

#### 1. Find-minimum mode（最小値検索モード）

ブロックが `true`/`false` を返す場合、`true` になる最小の要素のインデックスを返します。

```ruby
# 10以上の最小要素のインデックスを探す
[1, 5, 10, 15, 20].bsearch_index { |x| x >= 10 }
# => 2
```

#### 2. Find-any mode（任意検索モード）

ブロックが負数/0/正数を返す場合（<=> 演算子のような動作）、0 を返す要素のインデックスを返します。

```ruby
# 特定の値のインデックスを探す
[1, 5, 10, 15, 20].bsearch_index { |x| 10 <=> x }
# => 2
```

### 重要な注意点

1. **配列は事前にソート済みである必要があります**
   - ソートされていない配列では正しい結果が得られません

2. **O(log n) の時間計算量**
   - `index` メソッド（O(n)）よりも大きな配列では高速

3. **ブロックの条件は単調である必要があります**
   - false, false, ..., true, true, ... のパターン
   - または 負数, 負数, ..., 0, ..., 正数, 正数 のパターン

### パフォーマンスの考慮事項

```ruby
# 小さな配列の場合
small_array = [1, 2, 3, 4, 5]
# index の方がシンプルで十分

# 大きな配列の場合
large_array = (1..1000000).to_a
# bsearch_index の方が圧倒的に高速
```

### 関連メソッド

- `bsearch`: 要素そのものを返す
- `index`: 線形探索でインデックスを返す（O(n)）
- `find_index`: index のエイリアス
