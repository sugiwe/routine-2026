# Array#union

- 日付: 2026-04-07
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-union
- daily 記録: [daily/2026-04-07.md](../../daily/2026-04-07.md)

## 基本情報

```ruby
union(*other_arrays) → new_array
```

自身（`self`）と指定されたすべての配列（`other_arrays`）の要素を結合した新しい配列を返します。**重複を自動的に削除** し、**最初に見つかった要素を保持** します。要素の比較には `eql?` が使用されます。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

知らなかったかも、こんなメソッドあったんだ👀

### コード例

```ruby
# Returns a new array that is the union of the elements of self and all given arrays other_arrays; items are compared using eql?:
# 自身（self）と指定されたすべての配列（other_arrays）の要素を結合した新しい配列を返します。要素の比較には eql? が使用されます。
[0, 1, 2, 3].union([4, 5], [6, 7]) # => [0, 1, 2, 3, 4, 5, 6, 7]

# Removes duplicates (preserving the first found):
# 重複を削除する（最初に見つかったものは残す）：
[0, 1, 1].union([2, 1], [3, 1]) # => [0, 1, 2, 3]

# Preserves order (preserving the position of the first found):
# 順序を維持する（最初に見つかった位置を保持する）：
[3, 2, 1, 0].union([5, 3], [4, 2]) # => [3, 2, 1, 0, 5, 4]

# With no arguments given, returns a copy of self.
# 引数なしの場合は self のコピーを返す。
```

## 深掘り・補足

### 重要なポイント

#### 1. 非破壊的メソッド

`union` は元の配列を変更せず、新しい配列を返します。

```ruby
arr1 = [1, 2, 3]
arr2 = [3, 4, 5]
result = arr1.union(arr2)

arr1  # => [1, 2, 3]（変更なし）
result # => [1, 2, 3, 4, 5]（新しい配列）
```

#### 2. 重複の自動削除

複数の配列を結合する際、重複する要素は自動的に削除されます。**最初に見つかった要素が保持される** ため、順序が重要です。

```ruby
[1, 2, 2].union([2, 3, 3], [3, 4, 4])
# => [1, 2, 3, 4]
# 最初の 2 だけが残り、後続の 2 は削除される
```

#### 3. 複数配列の同時処理

`union` は複数の配列を一度に受け取ることができます。

```ruby
arr1 = [1, 2]
arr2 = [2, 3]
arr3 = [3, 4]

arr1.union(arr2, arr3)  # => [1, 2, 3, 4]
```

#### 4. 順序の保持

元の配列の順序が保持されます。新しい要素は末尾に追加されます。

```ruby
[3, 2, 1].union([6, 5, 4])
# => [3, 2, 1, 6, 5, 4]
```

### ユースケース

#### 1. 複数のリストをマージして重複を除外

```ruby
favorite_foods_user1 = ["寿司", "ラーメン", "カレー"]
favorite_foods_user2 = ["カレー", "ピザ", "寿司"]

all_favorites = favorite_foods_user1.union(favorite_foods_user2)
# => ["寿司", "ラーメン", "カレー", "ピザ"]
```

#### 2. タグやカテゴリの統合

```ruby
tags_article1 = ["Ruby", "Rails", "Web"]
tags_article2 = ["Ruby", "API", "Backend"]

all_tags = tags_article1.union(tags_article2)
# => ["Ruby", "Rails", "Web", "API", "Backend"]
```

#### 3. IDリストの統合

```ruby
user_ids_from_db1 = [1, 2, 3, 4]
user_ids_from_db2 = [3, 4, 5, 6]

all_user_ids = user_ids_from_db1.union(user_ids_from_db2)
# => [1, 2, 3, 4, 5, 6]
```

### 関連メソッドとの比較

| メソッド | 重複削除 | 複数配列対応 | 破壊的 | 用途 |
|---------|---------|-------------|--------|------|
| `union` | ✅ あり | ✅ あり | ❌ なし | 重複なしで複数配列を統合 |
| `+` | ❌ なし | ❌ なし（2つのみ） | ❌ なし | 単純な配列連結 |
| `\|` | ✅ あり | ❌ なし（2つのみ） | ❌ なし | 2つの配列の和集合 |
| `concat` | ❌ なし | ✅ あり | ✅ あり | 破壊的に配列を連結 |
| `uniq` | ✅ あり | ❌ なし | ❌ なし | 1つの配列の重複削除 |

#### `union` vs `|`（パイプ演算子）

`|` も重複を削除しますが、**2つの配列のみ** を扱います。

```ruby
# union: 複数配列を一度に処理
[1, 2].union([2, 3], [3, 4])  # => [1, 2, 3, 4]

# |: 2つの配列のみ
[1, 2] | [2, 3] | [3, 4]  # => [1, 2, 3, 4]（チェーン可能だが冗長）
```

複数の配列を統合する場合、`union` の方が簡潔です。

#### `union` vs `+`

`+` は重複を削除しません。

```ruby
[1, 2] + [2, 3]  # => [1, 2, 2, 3]（重複あり）
[1, 2].union([2, 3])  # => [1, 2, 3]（重複なし）
```

### パフォーマンスの考慮事項

`union` は内部で重複チェックを行うため、単純な `+` よりもやや遅いです。ただし、重複削除が必要な場合は、`(arr1 + arr2).uniq` よりも `arr1.union(arr2)` の方が効率的です。

```ruby
# 非効率
(arr1 + arr2 + arr3).uniq

# 効率的
arr1.union(arr2, arr3)
```

### まとめ

`Array#union` は、複数の配列を統合して重複を自動的に削除する便利なメソッドです。非破壊的で、順序を保持し、複数の配列を一度に処理できるため、タグやIDリストの統合など、実務でも頻繁に使用されます。
