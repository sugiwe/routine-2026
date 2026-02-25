# Array#keep_if

- 日付: 2026-02-26
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-keep_if
- daily 記録: [daily/2026-02-26.md](../../daily/2026-02-26.md)

## 基本情報

```ruby
keep_if {|element| ... } → self
keep_if → new_enumerator
```

ブロックが与えられた場合、自身の各要素に対してブロックを呼び出し、ブロックが真値を返さなかった要素は自身から削除します（破壊的メソッド）。

ブロックが与えられない場合は、新しい Enumerator を返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

（メモなし）

### コード例

```ruby
# keep_if {|element| ... } → self
# keep_if → new_enumerator
# With a block given, calls the block with each element of self; removes the element from self if the block does not return a truthy value:
# ブロックが与えられた場合、自身の各要素に対してブロックを呼び出し、ブロックが真値を返さなかった要素は自身から削除します。
a = [:foo, 'bar', 2, :bam]
a.keep_if {|element| element.to_s.start_with?('b') } # => ["bar", :bam]

# With no block given, returns a new Enumerator.
```

## 深掘り・補足

### 重要なポイント

1. **破壊的メソッド**: `keep_if` は元の配列を直接変更します
2. **条件に合う要素を保持**: ブロックが真値を返した要素のみ残します
3. **戻り値**: 修正後の配列自体（`self`）を返します
4. **ブロックなし**: Enumerator を返すので、チェーンメソッドとして使えます

### 公式ドキュメントの例

```ruby
arr = [1, 2, 3, 4, 5, 6]
arr.keep_if {|a| a < 4}
# => [1, 2, 3]
arr
# => [1, 2, 3]  # 元の配列が変更されている
```

### ユースケース

1. **条件フィルタリング（破壊的）**
```ruby
users = [{name: 'Alice', active: true}, {name: 'Bob', active: false}, {name: 'Carol', active: true}]
users.keep_if {|user| user[:active] }
# => [{name: 'Alice', active: true}, {name: 'Carol', active: true}]
```

2. **複雑な条件での絞り込み**
```ruby
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
numbers.keep_if {|n| n.even? && n > 4 }
# => [6, 8, 10]
```

3. **型による選別**
```ruby
mixed = [1, 'two', 3, 'four', 5]
mixed.keep_if {|item| item.is_a?(Integer) }
# => [1, 3, 5]
```

### 関連メソッドとの比較

#### `keep_if` vs `delete_if`

正反対の動作をします：

```ruby
arr1 = [1, 2, 3, 4, 5]
arr1.keep_if {|a| a < 4}
# => [1, 2, 3]  # 条件を満たす要素を保持

arr2 = [1, 2, 3, 4, 5]
arr2.delete_if {|a| a < 4}
# => [4, 5]  # 条件を満たす要素を削除
```

#### `keep_if` vs `select` / `select!`

| メソッド | 破壊的 | 戻り値 |
|---------|--------|--------|
| `keep_if` | ✓ | self |
| `select!` | ✓ | self または nil |
| `select` | ✗ | 新しい配列 |

```ruby
original = [1, 2, 3, 4, 5]

# keep_if: 破壊的、常に self を返す
arr1 = original.dup
result1 = arr1.keep_if {|a| a < 4}
# arr1 => [1, 2, 3]
# result1 => [1, 2, 3] (同じオブジェクト)

# select!: 破壊的、変更なしなら nil
arr2 = original.dup
result2 = arr2.select! {|a| a < 4}
# arr2 => [1, 2, 3]
# result2 => [1, 2, 3] (変更があったので self)

arr3 = [1, 2, 3]
result3 = arr3.select! {|a| a < 10}
# arr3 => [1, 2, 3]
# result3 => nil (変更がなかった)

# select: 非破壊的、新しい配列
arr4 = original.dup
result4 = arr4.select {|a| a < 4}
# arr4 => [1, 2, 3, 4, 5] (元の配列は変更されない)
# result4 => [1, 2, 3] (新しい配列)
```

### 注意点

1. **元の配列を保持したい場合**: `select` を使用する
2. **変更の有無を確認したい場合**: `select!` は変更がなければ `nil` を返す
3. **チェーンメソッド**: `keep_if` は self を返すので、さらにメソッドをチェーン可能

```ruby
arr = [1, 2, 3, 4, 5, 6]
arr.keep_if {|a| a < 5}.map {|a| a * 2}
# => [2, 4, 6, 8]
```

### パフォーマンスの考慮事項

- 破壊的メソッドなので、新しい配列を作成するオーバーヘッドがありません
- 大きな配列で元の配列が不要な場合は、`select` より効率的
- ただし、元の配列を保持する必要がある場合は `select` の方が安全
