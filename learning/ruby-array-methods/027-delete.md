# Array#delete

- 日付: 2026-01-27
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-delete

## 基本情報

```ruby
delete(object) → last_removed_object
delete(object) {|element| ... } → last_removed_object or block_return
```

配列内で指定されたオブジェクトと等しい**すべての要素**を削除する破壊的メソッドです。

## daily からの記録

### メモ

（daily には特記メモなし）

### コード例・補足

```ruby
# Removes zero or more elements from self.
# 自身から0個以上の要素を削除します。
# With no block given, removes from self each element ele such that ele == object; returns the last removed element:
# ブロックが指定されていない場合、要素eleがobjectと等しいすべての要素eleを自身から削除し、最後に削除された要素を返します。
a = [0, 1, 2, 2.0]
a.delete(2) # => 2.0
a           # => [0, 1]

# Returns nil if no elements removed:
# 要素が削除されなかった場合はnilを返します。
a.delete(2) # => nil

# With a block given, removes from self each element ele such that ele == object.
# ブロックが与えられた場合、要素 ele が object と等しい各要素を自身から削除します。
# If any such elements are found, ignores the block and returns the last removed element:
# これらの要素が見つかった場合、ブロックは無視され、最後に削除された要素が返されます。
a = [0, 1, 2, 2.0]
a.delete(2) {|element| fail 'Cannot happen' } # => 2.0
a                                             # => [0, 1]

# If no such element is found, returns the block's return value:
# 指定した要素が見つからない場合、ブロックの戻り値が返されます。
a.delete(2) {|element| "Element #{element} not found." }
# => "Element 2 not found."
```

## 深掘り・補足

### 重要なポイント

1. **一括削除**
   - 最初にマッチした要素だけでなく、**すべての一致する要素**を削除
   - `[1, 2, 2, 2, 3].delete(2)` は `[1, 3]` になる

2. **破壊的操作**
   - 元の配列を直接変更する
   - 非破壊版は存在しない（必要な場合は `dup` してから操作）

3. **返り値の挙動**
   - 要素が見つかった場合：削除された要素（複数削除されても最後の1つ）を返す
   - 要素が見つからない場合：`nil` を返す（ブロック未使用時）
   - ブロック使用時：要素が見つからない場合のみブロックを評価

4. **等価性の判定**
   - `==` 演算子で比較される
   - 上記の例で `2` と `2.0` がマッチするのはこのため

### ユースケース

#### 1. 特定値の完全除去

```ruby
# nil 値をすべて削除（compact! と同様の効果）
data = ['a', nil, 'b', nil, 'c']
data.delete(nil)
# => nil
# data は ['a', 'b', 'c']

# 特定のフラグ値を除去
flags = [true, false, true, false, true]
flags.delete(false)
# => false
# flags は [true, true, true]
```

#### 2. デフォルト値を使った安全な削除

```ruby
items = ['apple', 'banana', 'cherry']
result = items.delete('grape') { |item| "#{item} は見つかりませんでした" }
# => "grape は見つかりませんでした"

# nil を返したくない場合に便利
deleted = items.delete('banana') { 0 }
# => "banana" （要素が存在したのでブロックは無視される）
```

### 注意点

#### 1. 複数要素の削除と返り値

```ruby
arr = [1, 2, 2, 2, 3]
deleted = arr.delete(2)
# deleted は 2 （最後に削除された要素）
# arr は [1, 3]
# 削除された要素の数は返り値からはわからない
```

#### 2. ブロックは要素が見つからない場合のみ評価される

```ruby
count = 0
arr = [1, 2, 3]
arr.delete(2) { count += 1 }
# => 2
# count は 0 のまま（ブロックは実行されていない）

arr.delete(5) { count += 1 }
# => 1
# count は 1 になる（ブロックが実行された）
```

### 類似メソッドとの比較

| メソッド | 削除対象 | 破壊的 | 返り値 |
|---------|---------|--------|--------|
| `delete(obj)` | 値が一致する**すべて**の要素 | ○ | 削除された要素 or nil |
| `delete_at(index)` | 指定インデックスの**1つ**の要素 | ○ | 削除された要素 or nil |
| `delete_if {block}` | ブロック条件を満たす要素 | ○ | 配列自身 |
| `reject {block}` | ブロック条件を満たす要素 | × | 新しい配列 |

#### 使い分けの例

```ruby
arr = [1, 2, 3, 2, 4, 2]

# 値で削除（すべての 2 を削除）
arr.dup.delete(2)           # => [1, 3, 4]

# インデックスで削除（1番目の要素を削除）
arr.dup.delete_at(1)        # => [1, 3, 2, 4, 2]

# 条件で削除（偶数をすべて削除）
arr.dup.delete_if(&:even?)  # => [1, 3]
```

### パフォーマンスの考慮事項

- 配列を先頭から走査して一致する要素を探す
- 時間計算量：O(n)（n は配列の要素数）
- 削除のたびに配列の要素を詰める操作が発生するため、大量の要素削除には `delete_if` や `reject!` の方が効率的な場合がある

```ruby
# 多数の値を削除する場合
to_delete = [1, 2, 3]

# 非効率的
to_delete.each { |val| arr.delete(val) }  # 各削除で配列全体を走査

# 効率的
arr.delete_if { |val| to_delete.include?(val) }  # 1回の走査で完了
```