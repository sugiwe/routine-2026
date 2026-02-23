# Array#intersection

- 日付: 2026-02-24
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-intersection
- daily 記録: [daily/2026-02-24.md](../../daily/2026-02-24.md)

## 基本情報

```ruby
array.intersection(*other_arrays) → new_array
```

指定された配列のいずれかの要素と等しい（`eql?`）要素を `self` から抽出し、重複を除いて新しい配列として返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

（記録なし）

### コード例

```ruby
# Returns a new array containing each element in self that is eql? to at least one element in each of the given other_arrays; duplicates are omitted:
# 指定された配列のいずれかの要素と等しい（eql?）要素をselfから抽出し、重複を除いて新しい配列として返します。
[0, 0, 1, 1, 2, 3].intersection([0, 1, 2], [0, 1, 3]) # => [0, 1]

# Each element must correctly implement method hash.
# 各要素は、`hash`メソッドを正しく実装する必要があります。

# Order from self is preserved:
# 自己からの順序が保持されます。
[0, 1, 2].intersection([2, 1, 0]) # => [0, 1, 2]

# Returns a copy of self if no arguments are given.
# 引数を与えなかった場合、自身のコピーを返します。
["a", "b", "c"].intersection(["a"]) # => ["a"]
["a", "b", "c"].intersection # => ["a", "b", "c"]
```

## 深掘り・補足

### メソッドの動作

`intersection` は**すべての引数配列に共通する要素**を返します。これは数学の「積集合（intersection）」の概念に対応しています。

```ruby
# 3つの配列すべてに含まれる要素のみ
[0, 0, 1, 1, 2, 3].intersection([0, 1, 2], [0, 1, 3])
# => [0, 1]  # 2 は第3配列にない、3 は第2配列にない
```

### 重要な特徴

#### 1. 重複の除去（引数がある場合のみ）

**引数配列がある場合**、元の配列に重複があっても、結果には1つしか含まれません：

```ruby
[1, 1, 1, 2, 2].intersection([1, 2]) # => [1, 2]
```

**重要**: 引数なしの場合は、単に配列のコピーを返すだけで重複は除去されません：

```ruby
[1, 1, 2, 2].intersection # => [1, 1, 2, 2]  # 重複はそのまま
```

#### 2. 順序の保持

結果の配列は、**元の配列（self）の順序**を保持します：

```ruby
[2, 1, 0].intersection([0, 1, 2]) # => [2, 1, 0]
[0, 1, 2].intersection([2, 1, 0]) # => [0, 1, 2]
```

#### 3. `eql?` での比較

要素の比較には `eql?` メソッドが使われます。これは `==` よりも厳密で、型も考慮されます：

```ruby
[1, 2, 3].intersection([1.0, 2.0]) # => []
# 整数と浮動小数点数は eql? で false
```

#### 4. hash メソッドの要件

内部でハッシュテーブルを使うため、各要素は `hash` メソッドを正しく実装する必要があります：

```ruby
class CustomObject
  attr_reader :value

  def initialize(value)
    @value = value
  end

  def eql?(other)
    value == other.value
  end

  def hash
    value.hash  # 重要: eql? が true なら同じ hash 値を返す
  end
end

obj1 = CustomObject.new(1)
obj2 = CustomObject.new(1)
[obj1].intersection([obj2]) # => [obj1]
```

### 引数なしの場合

引数を渡さない場合は、元の配列の**浅いコピー（shallow copy）**を返します：

```ruby
["a", "b", "c"].intersection # => ["a", "b", "c"]
[1, 1, 2, 2].intersection    # => [1, 1, 2, 2]  # 重複もそのまま

# 浅いコピーなので、元の配列とは別オブジェクト
arr = [1, 2, 3]
copy = arr.intersection
copy.object_id == arr.object_id # => false
```

### 使用例

#### 複数のフィルター条件

```ruby
# ユーザーが選んだ複数の条件すべてに合う商品を検索
selected_by_color = ["prod1", "prod2", "prod5"]
selected_by_size = ["prod2", "prod5", "prod8"]
selected_by_price = ["prod2", "prod3", "prod5"]

available = selected_by_color.intersection(
  selected_by_size,
  selected_by_price
)
# => ["prod2", "prod5"]
```

#### 権限チェック

```ruby
user_roles = ["admin", "editor", "viewer"]
required_roles_1 = ["admin", "editor"]
required_roles_2 = ["editor", "moderator"]

# すべての必要な役割を持っているか
user_roles.intersection(required_roles_1).size == required_roles_1.size
# => true

user_roles.intersection(required_roles_2).size == required_roles_2.size
# => false  # moderator がない
```

### 関連メソッドとの比較

#### `&` 演算子（積集合）

`&` は2つの配列の積集合を返しますが、**1つの配列**としか使えません：

```ruby
[1, 2, 3] & [2, 3, 4]              # => [2, 3]
[1, 2, 3].intersection([2, 3, 4])  # => [2, 3]

# intersection は複数の配列を扱える
[1, 2, 3].intersection([2, 3, 4], [2, 3, 5])  # => [2, 3]
[1, 2, 3] & [2, 3, 4] & [2, 3, 5]             # これも可能だが連鎖が必要
```

#### `filter` / `select`

条件に基づいて要素を選択：

```ruby
[1, 2, 3, 4].filter { |x| x.even? }  # => [2, 4]
[1, 2, 3, 4].intersection([2, 4])    # => [2, 4]
```

`intersection` は集合演算、`filter` は条件判定です。

#### `uniq`

重複を除去したい場合は `uniq` を使います：

```ruby
[1, 1, 2, 2].uniq         # => [1, 2]
[1, 1, 2, 2].intersection # => [1, 1, 2, 2]
```

### パフォーマンスの考慮事項

- 内部でハッシュテーブルを使用するため、大きな配列でも効率的
- 時間計算量: O(n + m) （n は元の配列のサイズ、m は引数配列の合計サイズ）
- 元の配列の順序を保つため、ソートは不要

### 注意点

1. **破壊的メソッドはない** - `intersection` は常に新しい配列を返す
2. **nil や空配列の扱い** - 引数に空配列があると結果も空に

```ruby
[1, 2, 3].intersection([]) # => []
```

3. **大文字小文字の区別** - 文字列は厳密に比較される

```ruby
["A", "B"].intersection(["a", "b"]) # => []
```

4. **引数なしは重複除去しない** - コピーのみ（ユーザー検証済み）

```ruby
[1, 1, 2, 2].intersection # => [1, 1, 2, 2]
```

### まとめ

`intersection` は：
- ✅ 複数配列に共通する要素を抽出
- ✅ 引数ありの場合、重複を自動除去
- ✅ 引数なしの場合、浅いコピーを返す
- ✅ 元の配列の順序を保持
- ✅ `eql?` で厳密比較
- ✅ 効率的な実装（ハッシュテーブル使用）

集合演算が必要な場面で非常に便利なメソッドです。
