# Array#&

- 日付: 2026-01-01
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-26

## 基本情報

`self & other_array → new_array`

配列の積集合（共通要素）を返します。

## メモ（daily より）

```ruby
[0, 1, 2, 3] & [1, 2] # => [1, 2]

# Omits duplicates (重複を除外する):
[0, 1, 1, 0] & [0, 1] # => [0, 1]

# Preserves order from self (自身の順番を保つ):
[0, 1, 2] & [3, 2, 1, 0] # => [0, 1, 2]
[3, 2, 1, 0] & [0, 1, 2] # => [2, 1, 0] 左辺側の順番になるってことか👀
```

## 深掘り・補足

### 重要なポイント

1. **順序の保持**: 左辺の配列（`self`）の要素の順番が保たれます
2. **重複の除去**: 結果には重複する要素が含まれません（左辺に重複があっても1つになります）
3. **新しい配列**: 元の配列は変更されず、新しい配列が返されます

### ユースケース

- 2つのリストから共通項目を抽出したい場合
- フィルタリングと重複排除を同時に行いたい場合

```ruby
# 例: ユーザーの権限チェック
user_permissions = [:read, :write, :read, :delete]
required_permissions = [:read, :write]
has_permissions = (user_permissions & required_permissions) == required_permissions
# => true
```

### 注意点

- 内部的にハッシュテーブルを使用するため、要素は `eql?` と `hash` メソッドで比較されます
- 大きな配列の場合、パフォーマンスは O(n+m) 程度ですが、小さい配列を右辺に置くと効率的です
