# Array#transpose

- 日付: 2026-04-06
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-transpose
- daily 記録: [daily/2026-04-06.md](../../daily/2026-04-06.md)

## 基本情報

```ruby
transpose → new_array
```

**自身を転置行列とした新しい配列を返す**非破壊的メソッドです。

転置行列とは、行と列を入れ替えた行列のこと。2次元配列の「行」と「列」を交換します。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# Returns a new array that is self as a transposed matrix:
# 自身を転置行列とした新しい配列を返します。
a = [[:a0, :a1], [:b0, :b1], [:c0, :c1]]
a.transpose # => [[:a0, :b0, :c0], [:a1, :b1, :c1]]

# The elements of self must all be the same size.
# Related: see Methods for Converting.
```

## 深掘り・補足

### 動作の仕組み

元の配列の**行**と**列**を入れ替えます：

```ruby
# 元の配列（3行2列）
[
  [:a0, :a1],  # 1行目
  [:b0, :b1],  # 2行目
  [:c0, :c1]   # 3行目
]

# 転置後（2行3列）
[
  [:a0, :b0, :c0],  # 元の1列目
  [:a1, :b1, :c1]   # 元の2列目
]
```

### 重要なポイント

#### 1. **すべての要素が同じサイズでなければならない**

```ruby
# ✅ OK: すべて2要素
[[1, 2], [3, 4], [5, 6]].transpose
# => [[1, 3, 5], [2, 4, 6]]

# ❌ NG: サイズが異なる
[[1, 2], [3, 4, 5]].transpose
# => IndexError (element size differs)
```

#### 2. **非破壊的メソッド**

元の配列は変更されません：

```ruby
a = [[1, 2], [3, 4]]
b = a.transpose
a # => [[1, 2], [3, 4]] （変更なし）
b # => [[1, 3], [2, 4]]
```

#### 3. **ネストした配列が必須**

トップレベルの配列の各要素は配列でなければなりません：

```ruby
# ❌ NG: 要素が配列ではない
[1, 2, 3].transpose
# => TypeError (no implicit conversion of Integer into Array)
```

### 実践的なユースケース

#### 1. **行列データの行列変換**

数学的な行列操作や、表データの行列入れ替えに使えます：

```ruby
# CSVデータのような表を転置
data = [
  ["Name", "Alice", "Bob"],
  ["Age", 25, 30],
  ["City", "Tokyo", "Osaka"]
]

data.transpose
# => [
#   ["Name", "Age", "City"],
#   ["Alice", 25, "Tokyo"],
#   ["Bob", 30, "Osaka"]
# ]
```

#### 2. **複数配列のZIP的な操作**

複数の配列を「まとめて取り出す」ときに便利：

```ruby
keys = [:name, :age, :city]
values1 = ["Alice", 25, "Tokyo"]
values2 = ["Bob", 30, "Osaka"]

[keys, values1, values2].transpose
# => [
#   [:name, "Alice", "Bob"],
#   [:age, 25, 30],
#   [:city, "Tokyo", "Osaka"]
# ]
```

#### 3. **座標データの変換**

XY座標のペアを、X座標のリストとY座標のリストに分けるときなど：

```ruby
points = [[1, 2], [3, 4], [5, 6]]
x_coords, y_coords = points.transpose

x_coords # => [1, 3, 5]
y_coords # => [2, 4, 6]
```

### 注意点

- **空配列の場合**: `[].transpose` は `[]` を返します
- **1行の場合**: `[[1, 2, 3]].transpose` は `[[1], [2], [3]]` を返します（各要素が1要素の配列になる）
- **パフォーマンス**: 大きな配列では新しい配列を生成するためメモリを消費します

### 関連メソッド

- `Array#zip`: 複数の配列を組み合わせる（transposeに似た動作）
- `Array#flatten`: ネストした配列を平坦化する
- `Array#each_slice`: 配列を指定サイズのチャンクに分割（逆方向の操作）

### 数学的背景

転置行列は線形代数の基本操作の一つです。数学では行列 A の転置を A^T と表記します。

性質：
- `(A^T)^T = A`（転置の転置は元に戻る）
- Ruby でも同様：`a.transpose.transpose == a`

```ruby
a = [[1, 2], [3, 4]]
a.transpose.transpose == a  # => true
```
