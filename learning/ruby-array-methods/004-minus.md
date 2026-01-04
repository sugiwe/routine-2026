# Array#- (差集合)

**学習日**: 2026-01-04
**URL**: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-2D

## 基本情報

```ruby
self - other_array → new_array
```

self の要素のうち、other_array に存在しないもののみを含む新しい配列を返します。self からの順序は保持されます。

## メモ（daily より）

```ruby
# Returns a new array containing only those elements of self that are not found in other_array; the order from self is preserved:
# self の要素のうち、other_array に存在しないもののみを含む新しい配列を返します。self からの順序は保持されます:
[0, 1, 1, 2, 1, 1, 3, 1, 1] - [1]             # => [0, 2, 3]
[0, 1, 1, 2, 1, 1, 3, 1, 1] - [3, 2, 0, :foo] # => [1, 1, 1, 1, 1, 1]
[0, 1, 2] - [:foo]                            # => [0, 1, 2]

# Element are compared using method eql? (as defined in each element of self).
# 要素はメソッド eql? を使用して比較されます（self の各要素で定義されている通り）。
```

## 深掘り・補足

### 重要なポイント

1. **差集合演算**: 数学の集合演算における差集合（A - B）と同じ概念
2. **重複削除**: 結果から削除される要素は、元の配列に何個あっても全て削除される
3. **順序保持**: self の元の順序は保持される
4. **非破壊的**: 元の配列は変更されず、新しい配列を返す

### ユースケース

```ruby
# ブラックリストによるフィルタリング
all_users = ['alice', 'bob', 'charlie', 'dave']
blocked_users = ['bob', 'dave']
active_users = all_users - blocked_users  # => ['alice', 'charlie']

# 既読アイテムを除外
all_items = [1, 2, 3, 4, 5]
read_items = [2, 4]
unread_items = all_items - read_items  # => [1, 3, 5]

# 複数の条件で除外
tags = ['ruby', 'rails', 'javascript', 'react', 'vue']
exclude = ['javascript', 'react', 'vue']
backend_tags = tags - exclude  # => ['ruby', 'rails']
```

### 注意点

- **比較方法**: `eql?` メソッドで比較されるため、同じ値でも型が違えば別物として扱われる
  ```ruby
  [1, 2, 3] - [1.0]  # => [1, 2, 3] (整数と浮動小数点は別)
  ```

- **パフォーマンス**: 内部的には other_array を走査するため、大きな配列では Set を使う方が高速
  ```ruby
  # 大量データの場合は Set の方が効率的
  require 'set'
  result = array.to_set - other_array.to_set
  ```

### 関連メソッドとの比較

- `Array#&` (積集合): 両方に存在する要素のみを返す
- `Array#|` (和集合): 両方の要素を重複なく結合
- `Array#delete_if` / `Array#reject`: 条件に基づいて要素を削除
