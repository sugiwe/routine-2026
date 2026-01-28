# Array#delete_at

- 日付: 2026-01-28
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-delete_at

## 基本情報

```ruby
delete_at(index) → removed_object or nil
```

指定されたインデックス位置にある要素を配列から削除し、削除された要素を返します。インデックスが範囲外の場合は `nil` を返します。

## daily からの記録

### メモ

`delete_at(index) → removed_object or nil`

### コード例・補足

```ruby
# Removes the element of self at the given index, which must be an integer-convertible object.
# 指定されたインデックスにある要素を削除します。インデックスは整数に変換可能なオブジェクトである必要があります。
# When index is non-negative, deletes the element at offset index:
# インデックスが負でない場合、オフセットインデックスにある要素を削除します。
a = [:foo, 'bar', 2]
a.delete_at(1) # => "bar"
a # => [:foo, 2]

# When index is negative, counts backward from the end of the array:
# インデックスが負の場合、配列の末尾から逆方向にカウントされます。
a = [:foo, 'bar', 2]
a.delete_at(-2) # => "bar"
a # => [:foo, 2]

# When index is out of range, returns nil.
# インデックスが範囲外の場合、nilを返します。
a = [:foo, 'bar', 2]
a.delete_at(3)  # => nil
a.delete_at(-4) # => nil

# Related: see Methods for Deleting.
```

## 深掘り・補足

### 重要なポイント

1. **破壊的メソッド**
   - 元の配列を変更します（非破壊的なバージョンはありません）
   - 変更を加えたくない場合は、事前に配列を複製してください

2. **戻り値の活用**
   - 削除された要素を返すため、削除と値の取得を同時に行えます
   - 範囲外の場合は `nil` を返すため、存在確認にも使えます

3. **負のインデックス**
   - `-1` は最後の要素、`-2` は最後から2番目の要素を指します
   - 負のインデックスも範囲外チェックの対象です

### ユースケース

```ruby
# ケース1: 特定位置の要素を削除して取得
tasks = ['朝食', '仕事', '昼食', '会議', '夕食']
lunch = tasks.delete_at(2)  # => '昼食'
tasks  # => ['朝食', '仕事', '会議', '夕食']

# ケース2: 最後の要素を削除（pop の代替）
numbers = [1, 2, 3, 4, 5]
last = numbers.delete_at(-1)  # => 5
numbers  # => [1, 2, 3, 4]

# ケース3: 安全な削除（範囲外チェック）
items = ['a', 'b', 'c']
removed = items.delete_at(10)
if removed
  puts "削除しました: #{removed}"
else
  puts "指定されたインデックスは存在しません"
end
```

### 関連メソッドとの比較

| メソッド | 削除対象 | 戻り値 | 用途 |
|---------|---------|--------|------|
| `delete(obj)` | 値で指定した全要素 | 削除した値 or nil | 特定の値をすべて削除 |
| `delete_at(index)` | インデックスで指定した1要素 | 削除した値 or nil | 特定位置の要素を削除 |
| `pop` | 末尾の要素 | 削除した値 or nil | スタック操作 |
| `shift` | 先頭の要素 | 削除した値 or nil | キュー操作 |
| `slice!(index)` | インデックスで指定 | 削除した値 or nil | `delete_at` のエイリアス的 |

```ruby
# delete vs delete_at の違い
arr1 = [1, 2, 3, 2, 4]
arr1.delete(2)      # => 2（値2をすべて削除）
arr1                # => [1, 3, 4]

arr2 = [1, 2, 3, 2, 4]
arr2.delete_at(1)   # => 2（インデックス1の要素のみ削除）
arr2                # => [1, 3, 2, 4]
```

### 注意点とベストプラクティス

1. **範囲外アクセス**
   ```ruby
   arr = [1, 2, 3]
   arr.delete_at(10)  # => nil（エラーにはならない）

   # エラーを発生させたい場合は fetch を使う
   arr.fetch(10)  # => IndexError
   ```

2. **連続削除の落とし穴**
   ```ruby
   # ❌ 悪い例：インデックスがずれる
   arr = [1, 2, 3, 4, 5]
   arr.delete_at(1)  # => 2, arr = [1, 3, 4, 5]
   arr.delete_at(1)  # => 3（元のインデックス2の要素）

   # ✅ 良い例：後ろから削除する、または reject を使う
   arr = [1, 2, 3, 4, 5]
   arr.delete_at(2)
   arr.delete_at(1)

   # または
   arr = [1, 2, 3, 4, 5]
   arr.reject.with_index { |_, i| [1, 2].include?(i) }
   ```

3. **パフォーマンス**
   - 配列の先頭や中間を削除すると、後続の要素をシフトするため O(n) の時間がかかります
   - 末尾の削除（`delete_at(-1)` や `pop`）は O(1) で高速です

### 実践的なパターン

```ruby
# パターン1: 条件に合う最初の要素を削除
users = [{id: 1, name: 'Alice'}, {id: 2, name: 'Bob'}, {id: 3, name: 'Charlie'}]
index = users.index { |u| u[:id] == 2 }
users.delete_at(index) if index  # => {id: 2, name: 'Bob'}

# パターン2: 削除結果のログ記録
def remove_task(tasks, index)
  removed = tasks.delete_at(index)
  if removed
    puts "タスク「#{removed}」を削除しました"
  else
    puts "指定されたインデックス #{index} は無効です"
  end
  removed
end
```
