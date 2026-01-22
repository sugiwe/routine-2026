# Array#compact

- 日付: 2026-01-22
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-compact

## 基本情報

```ruby
compact → new_array
compact! → self or nil
```

`compact` は、すべての `nil` 要素を含まない新しい配列を返します。破壊的版の `compact!` も存在し、元の配列を直接変更します。

## daily からの記録

### メモ

`compact → new_array`

### コード例・補足

```ruby
# Returns a new array containing only the non-nil elements from self; element order is preserved:
# nilでない要素のみを含む新しい配列を返します。要素の順序は保持されます。
a = [nil, 0, nil, false, nil, '', nil, [], nil, {}]
a.compact # => [0, false, "", [], {}]
```

## 深掘り・補足

### 重要なポイント

1. **非破壊的操作**
   - `compact` は元の配列を変更せず、新しい配列を返す
   - 元の配列はそのまま保持される

2. **破壊的操作**
   - `compact!` は配列を直接修正する
   - 変更があった場合は `self`（自身）を返す
   - 変更がなかった場合（nil 要素がなかった場合）は `nil` を返す

3. **nil 要素のみ削除**
   - `false`、`0`、空文字列 `""`、空配列 `[]`、空ハッシュ `{}` などは削除**されない**
   - Ruby では `nil` のみが「nil 値」として扱われる

### ユースケース

**1. データベースクエリの結果から未設定値を除外**
```ruby
users = [
  { name: "Alice", email: "alice@example.com" },
  { name: "Bob", email: nil },
  { name: "Charlie", email: "charlie@example.com" }
]
emails = users.map { |u| u[:email] }.compact
# => ["alice@example.com", "charlie@example.com"]
```

**2. ユーザー入力の検証後の正規化**
```ruby
input = ["foo", nil, "bar", nil, "baz"]
valid_input = input.compact
# => ["foo", "bar", "baz"]
```

**3. API レスポンスから null 値を削除**
```ruby
response = [{ id: 1 }, nil, { id: 2 }, nil, { id: 3 }]
valid_responses = response.compact
# => [{ id: 1 }, { id: 2 }, { id: 3 }]
```

### 注意点

**falsy な値と nil の違い**
```ruby
a = [nil, false, 0, "", [], {}]
a.compact # => [false, 0, "", [], {}]
```

`false` や `0` は `nil` ではないため削除されません。これらも除外したい場合は `select` や `reject` を使用します：

```ruby
# 真理値で判定したい場合
a.select { |x| x } # => [[], {}]  # false と 0 と "" は除外される

# 空でない要素のみを保持したい場合
a.reject { |x| x.nil? || x == false || x == 0 || x == "" }
# => [[], {}]
```

### 関連メソッドとの比較

| メソッド | 機能 | 用途 |
|---------|------|------|
| `compact` | nil 要素を削除 | nil 値の除外 |
| `select` | 条件に合う要素を選択 | 柔軟なフィルタリング |
| `reject` | 条件に合わない要素を保持 | 特定要素の除外 |
| `uniq` | 重複要素を削除 | 一意な要素のみを保持 |

**使い分け例:**
```ruby
a = [1, nil, 2, nil, 3, 3]

a.compact      # => [1, 2, 3, 3]  (nil のみ削除)
a.uniq         # => [1, nil, 2, 3]  (重複削除)
a.compact.uniq # => [1, 2, 3]  (nil 削除 + 重複削除)
```

### パフォーマンスの考慮事項

**非破壊的 vs 破壊的**
```ruby
# 非破壊的（メモリを消費）
large_array = [1, nil, 2, nil, 3] * 1000
result = large_array.compact  # 新しい配列を作成

# 破壊的（メモリ効率が良い）
large_array.compact!  # 元の配列を変更
```

- 大規模な配列で繰り返し呼び出す場合は `compact!` を検討
- ただし、元の配列を保持する必要がある場合は `compact` を使用
- `compact!` は変更がなかった場合に `nil` を返すことに注意

**チェーンメソッドでの使用**
```ruby
# compact は新しい配列を返すため、チェーンメソッドに最適
result = array
  .compact
  .map { |x| x * 2 }
  .select { |x| x > 10 }
```

### 実践のヒント

- `nil` のみを削除したい場合は `compact` が最もシンプル
- より複雑な条件でフィルタリングしたい場合は `select` や `reject` を使用
- 破壊的操作が必要かどうかを検討し、適切なメソッドを選択
- `compact!` の戻り値が `nil` の場合（変更なし）を考慮したエラーハンドリングを行う
