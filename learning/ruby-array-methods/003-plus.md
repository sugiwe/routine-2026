# Array#+

- 日付: 2026-01-03
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-2B

## 基本情報

`self + other_array → new_array`

2 つの配列を連結した新しい配列を返します。

## メモ（daily より）

```ruby
# Returns a new array containing all elements of self followed by all elements of other_array:
# self の全要素に続いて other_array の全要素を含む新しい配列を返します:
a = [0, 1] + [2, 3]
a # => [0, 1, 2, 3]
```

## 深掘り・補足

### 重要なポイント

1. **非破壊的**: 元の配列は変更されず、新しい配列が返されます
2. **順序の保持**: 左辺の配列の要素が先、右辺の配列の要素が後に続きます
3. **型の要件**: 引数は配列でなければなりません（他の Enumerable では動作しません）

### ユースケース

```ruby
# 複数のリストを結合
admin_users = ['alice', 'bob']
regular_users = ['charlie', 'dave']
all_users = admin_users + regular_users
# => ['alice', 'bob', 'charlie', 'dave']

# 要素の追加（ただし非効率）
items = [1, 2, 3]
items = items + [4]  # 新しい配列が作成される
# => [1, 2, 3, 4]
```

### 他のメソッドとの比較

#### `concat` との違い

```ruby
# + は非破壊的
a = [1, 2]
b = a + [3, 4]
a  # => [1, 2] （変更されない）
b  # => [1, 2, 3, 4]

# concat は破壊的
a = [1, 2]
a.concat([3, 4])
a  # => [1, 2, 3, 4] （変更される）
```

#### `<<` との違い

```ruby
# + は配列全体を連結
[1, 2] + [3, 4]  # => [1, 2, 3, 4]

# << は要素（または配列そのもの）を追加
[1, 2] << [3, 4]  # => [1, 2, [3, 4]] （ネストされる）
```

### 注意点

- **パフォーマンス**: 新しい配列を作成するため、大きな配列の場合はメモリと時間のコストがかかります
- **連続使用**: `a + b + c` のような連続使用では、中間配列が複数作成されるため非効率です

  ```ruby
  # 非効率
  result = [] + arr1 + arr2 + arr3

  # より効率的
  result = [arr1, arr2, arr3].flatten
  # または
  result = arr1.dup.concat(arr2).concat(arr3)
  ```

### 引数の型エラー

```ruby
[1, 2] + 3        # TypeError: no implicit conversion of Integer into Array
[1, 2] + [3]      # => [1, 2, 3] （OK）
[1, 2] + (3..5)   # TypeError: no implicit conversion of Range into Array
```

配列以外を連結したい場合は、明示的に配列に変換する必要があります：

```ruby
[1, 2] + [3]           # => [1, 2, 3]
[1, 2] + (3..5).to_a   # => [1, 2, 3, 4, 5]
```
