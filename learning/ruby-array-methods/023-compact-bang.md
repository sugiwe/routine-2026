# Array#compact!

- 日付: 2026-01-23
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-compact-21

## 基本情報

```ruby
compact! → self or nil
```

配列内のすべての `nil` 要素を削除する破壊的メソッド。元の配列を直接変更します。

## daily からの記録

### メモ

`compact! → self or nil`

### コード例・補足

```ruby
# Removes all nil elements from self; Returns self if any elements are removed, nil otherwise:
# nilの要素をすべて削除します。要素が削除された場合はselfを返し、それ以外の場合はnilを返します。
a = [nil, 0, nil, false, nil, '', nil, [], nil, {}]
a.compact! # => [0, false, "", [], {}]
a          # => [0, false, "", [], {}]
a.compact! # => nil
```

## 深掘り・補足

### 重要なポイント

1. **破壊的メソッド**
   - 元の配列を直接変更する（`!` が付いている）
   - メモリ効率が良い（新しい配列を作らない）

2. **nil のみを削除**
   - `nil` 以外の falsy な値（`false`, `0`, `""`, `[]`, `{}`）は残る
   - 空文字列や空配列は削除されない点に注意

3. **特殊な返り値**
   - 変更があった場合：`self`（変更後の配列自体）を返す
   - 変更がなかった場合：`nil` を返す
   - この仕様により、変更があったかどうかを判定できる

### ユースケース

```ruby
# API レスポンスから nil を除去
user_data = {
  name: "Alice",
  email: nil,
  age: 30,
  phone: nil
}
values = user_data.values
values.compact!  # => ["Alice", 30]

# 変更があったかチェック
items = [1, 2, 3]
if items.compact!
  puts "nil が見つかりました"
else
  puts "nil はありませんでした"  # こちらが実行される
end
```

### 注意点

1. **返り値の扱い**
   ```ruby
   # ❌ 間違った使い方
   result = [nil, 1, nil, 2].compact!
   # result は配列だが、nil がなければ nil が返る可能性がある

   # ✅ 正しい使い方
   arr = [nil, 1, nil, 2]
   arr.compact!
   # arr を使う（返り値ではなく配列自体を使う）
   ```

2. **フリーズされた配列**
   ```ruby
   arr = [nil, 1, 2].freeze
   arr.compact!  # FrozenError: can't modify frozen Array
   ```

3. **空配列との違い**
   ```ruby
   [nil, nil].compact!  # => []（空配列を返す）
   [].compact!          # => nil（変更なし）
   ```

### 関連メソッドとの比較

| メソッド | 破壊的 | 返り値 | 使い分け |
|---------|-------|--------|---------|
| `compact` | ❌ | 新しい配列（常に配列） | 元の配列を保持したい場合 |
| `compact!` | ✅ | self または nil | メモリ効率を優先、変更検出が必要な場合 |

```ruby
# compact（非破壊的）
arr1 = [nil, 1, nil, 2]
new_arr = arr1.compact  # => [1, 2]
arr1                     # => [nil, 1, nil, 2]（変更なし）

# compact!（破壊的）
arr2 = [nil, 1, nil, 2]
arr2.compact!            # => [1, 2]
arr2                     # => [1, 2]（変更された）
```

### パフォーマンスの考慮事項

- `compact!` は新しい配列を作成しないため、大量のデータを扱う場合はメモリ効率が良い
- ただし、元の配列が必要な場合は `compact` を使用すべき
- 変更の有無を検出したい場合は、返り値が `nil` かどうかをチェックできる

### 実践的な使用例

```ruby
# フォームデータのクリーニング
form_data = ["Alice", "", nil, "Bob", nil, "Charlie"]
form_data.compact!  # => ["Alice", "", "Bob", "Charlie"]
# 空文字列は残り、nil のみ削除される

# ログデータの整形
logs = [
  { message: "Error", level: 3 },
  nil,
  { message: "Warning", level: 2 },
  nil
]
logs.compact!  # => [{ message: "Error", level: 3 }, { message: "Warning", level: 2 }]
```
