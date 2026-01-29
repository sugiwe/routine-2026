# Array#delete_if

- 日付: 2026-01-29
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-delete_if

## 基本情報

```ruby
delete_if {|element| ... } → self
delete_if → new_enumerator
```

**破壊的メソッド**: ブロックで指定された条件を満たす要素を配列から削除します。ブロックが真を返す要素が削除対象となります。

## daily からの記録

### メモ

（メモなし）

### コード例・補足

```ruby
# delete_if {|element| ... } → self
# delete_if → new_numerator

# With a block given, calls the block with each element of self; removes the element if the block returns a truthy value; returns self:
# ブロックが与えられた場合、自身の各要素に対してブロックを呼び出し、ブロックが真値を返した場合にその要素を削除し、自身を返します。
a = [:foo, 'bar', 2, 'bat']
a.delete_if {|element| element.to_s.start_with?('b') } # => [:foo, 2]

# With no block given, returns a new Enumerator.
# ブロックが与えられない場合、新しいEnumeratorを返します。
```

## 深掘り・補足

### 重要なポイント

1. **破壊的操作**
   - 元の配列を直接変更します
   - 元のデータが必要な場合は事前に `dup` や `clone` で複製が必要

2. **戻り値は常に `self`**
   - 要素が削除されたかどうかに関わらず、変更された配列自体を返す
   - メソッドチェーンが可能

3. **ブロックなしの場合**
   - Enumerator を返すため、遅延評価やメソッドチェーンに活用できる

### ユースケース

```ruby
# 1. 条件に合う要素を削除
arr = [1, 2, 3, 4, 5, 6]
arr.delete_if { |a| a < 4 }   #=> [4, 5, 6]
arr                           #=> [4, 5, 6] (元の配列が変更されている)

# 2. 文字列配列から特定パターンを削除
names = ['Alice', 'Bob', 'Charlie', 'David']
names.delete_if { |name| name.length < 5 }  #=> ['Alice', 'Charlie', 'David']

# 3. Enumerator として使用
arr = [1, 2, 3, 4, 5]
enum = arr.delete_if  # Enumerator を取得
enum.each { |n| n.even? }  #=> [1, 3, 5]
```

### 注意点

1. **反復中の変更は危険**
   ```ruby
   # 避けるべきパターン
   arr = [1, 2, 3, 4, 5]
   arr.delete_if do |n|
     arr << 10  # ブロック内での配列変更は予測不可能
     n < 3
   end
   ```

2. **破壊的操作の影響**
   ```ruby
   original = [1, 2, 3, 4]
   reference = original
   original.delete_if { |n| n < 3 }
   # original と reference の両方が [3, 4] になる
   ```

3. **空の配列でも `self` を返す**
   ```ruby
   arr = [1, 2, 3]
   arr.delete_if { |n| n > 10 }  #=> [1, 2, 3] (何も削除されていない)
   ```

### 関連メソッドとの比較

| メソッド | 特性 | 戻り値 | 用途 |
|---------|------|--------|------|
| `delete_if` | 破壊的、条件が真で削除 | 常に `self` | 元の配列を変更したい場合 |
| `keep_if` | 破壊的、条件が真で保持 | 常に `self` | 逆の条件で保持したい場合 |
| `reject!` | 破壊的、条件が真で削除 | `self` または `nil` | 変更があったか判定したい場合 |
| `reject` | 非破壊的、条件が真で削除 | 新しい配列 | 元の配列を保持したい場合 |

#### `delete_if` vs `reject!`

```ruby
# delete_if は常に self を返す
arr1 = [1, 2, 3]
result1 = arr1.delete_if { |n| n > 10 }  #=> [1, 2, 3]

# reject! は変更がなければ nil を返す
arr2 = [1, 2, 3]
result2 = arr2.reject! { |n| n > 10 }    #=> nil

# これにより、変更があったかどうかを判定できる
if arr2.reject! { |n| n < 2 }
  puts "配列が変更されました"
else
  puts "配列は変更されませんでした"
end
```

### パフォーマンスの考慮事項

- **メモリ効率**: 原地（in-place）で動作するため、新しい配列を作成しない
- **時間計算量**: O(n) - 配列の各要素に対してブロックを実行
- **大規模配列**: 複雑なブロック処理の場合、実行時間が線形時間で増加するため注意

### 実践的なヒント

1. **非破壊的な操作が必要な場合は `reject` を使う**
   ```ruby
   original = [1, 2, 3, 4, 5]
   filtered = original.reject { |n| n < 3 }  #=> [3, 4, 5]
   original  #=> [1, 2, 3, 4, 5] (元の配列は変更されていない)
   ```

2. **変更の有無を確認したい場合は `reject!` を使う**
   ```ruby
   arr = [1, 2, 3, 4, 5]
   if arr.reject! { |n| n < 3 }
     puts "要素が削除されました: #{arr}"
   else
     puts "削除する要素がありませんでした"
   end
   ```

3. **メソッドチェーンで活用**
   ```ruby
   arr = [1, 2, 3, 4, 5, 6]
   arr.delete_if { |n| n.even? }.map { |n| n * 2 }  #=> [2, 6, 10]
   ```
