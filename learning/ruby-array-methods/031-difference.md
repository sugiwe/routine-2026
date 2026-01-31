# Array#difference

- 日付: 2026-01-31
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-difference
- daily 記録: [daily/2026-01-31.md](../../daily/2026-01-31.md)

## 基本情報

```ruby
difference(*other_arrays = []) → new_array
```

指定された配列（`other_arrays`）のいずれにも存在しない要素のみを、`self` から抽出して新しい配列として返します。要素の比較には `eql?` が用いられ、`self` の順序が保持されます。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

`difference(*other_arrays = []) → new_array`

### コード例

```ruby
# Returns a new array containing only those elements from self that are not found in any of the given other_arrays; items are compared using eql?; order from self is preserved:
# 指定された配列(other_arrays)のいずれにも存在しない要素のみを、selfから抽出して新しい配列として返します。要素の比較には`eql?`が用いられ、selfの順序が保持されます。
[0, 1, 1, 2, 1, 1, 3, 1, 1].difference([1]) # => [0, 2, 3]
[0, 1, 2, 3].difference([3, 0], [1, 3])     # => [2]
[0, 1, 2].difference([4])                   # => [0, 1, 2]
[0, 1, 2].difference                        # => [0, 1, 2]

# Returns a copy of self if no arguments are given.
# 引数がない場合は、自身のコピーを返します。
```

## 深掘り・補足

### 重要なポイント

1. **非破壊的操作**: 元の配列 `self` は変更されず、新しい配列が返される
2. **複数配列対応**: 複数の配列を引数として受け取り、それらすべてに含まれない要素を抽出
3. **除外対象の完全除去**: 引数で指定した要素は、元の配列内に何個あってもすべて除去される
4. **重複の保持**: 除外対象以外の要素で重複があれば、そのまま保持される
5. **順序の保持**: 元の配列の順序が保たれる
6. **等値性判定**: `eql?` メソッドを使用して要素を比較

### 除外と重複の挙動

```ruby
# 引数で指定した要素（1）はすべて除去されるが、残った要素の重複は保持
[0, 1, 1, 2, 1, 1, 3, 1, 1, 3, 2].difference([1])
# => [0, 2, 3, 3, 2]  （2 と 3 の重複は残る）

# 公式ドキュメントの例を再確認
[0, 1, 1, 2, 1, 1, 3, 1, 1].difference([1])
# => [0, 2, 3]  （この例では結果的に重複がないだけ）
```

### ユースケース

#### 1. リストから不要な要素を除外

```ruby
# 全ユーザーから管理者とゲストを除外
all_users = ['alice', 'bob', 'charlie', 'admin', 'guest', 'bob']
excluded = ['admin', 'guest']
regular_users = all_users.difference(excluded)
# => ['alice', 'bob', 'charlie', 'bob']  （bob の重複は保持）
```

#### 2. 複数の除外リストを適用

```ruby
# 商品リストから売り切れと予約済みを除外
products = ['A', 'B', 'C', 'D', 'E']
sold_out = ['A', 'C']
reserved = ['B', 'E']
available = products.difference(sold_out, reserved)
# => ['D']
```

#### 3. データフィルタリング

```ruby
# タグから除外したいものを取り除く
all_tags = ['ruby', 'python', 'deprecated', 'java', 'legacy', 'ruby']
deprecated_tags = ['deprecated', 'legacy']
active_tags = all_tags.difference(deprecated_tags)
# => ['ruby', 'python', 'java', 'ruby']  （ruby の重複は保持）
```

### 関連メソッドとの比較

#### `difference` vs `-`（配列の差）

```ruby
[1, 2, 3].difference([2])  # => [1, 3]
[1, 2, 3] - [2]            # => [1, 3]
```

`-` は単一の配列しか受け取れませんが、`difference` は複数受け取れます：

```ruby
[1, 2, 3].difference([1], [3])  # => [2]
# `-` では以下のように書く必要がある
[1, 2, 3] - [1] - [3]           # => [2]
```

#### `difference` vs `select`/`reject`

```ruby
# difference: 除外リストを使用
[1, 2, 3, 4].difference([2, 4])  # => [1, 3]

# reject: 条件で除外
[1, 2, 3, 4].reject { |n| [2, 4].include?(n) }  # => [1, 3]

# select: 条件で選択
[1, 2, 3, 4].select { |n| ![2, 4].include?(n) }  # => [1, 3]
```

`difference` は除外リストが明確な場合に簡潔でわかりやすいです。

### 注意点

1. **重複の扱い**: 除外対象以外の重複は保持される。重複も取り除きたい場合は `uniq` を併用

```ruby
[1, 2, 2, 3, 3].difference([1]).uniq  # => [2, 3]
```

2. **パフォーマンス**: 大規模な配列では `Set` の使用を検討した方が良い場合がある
3. **等値性判定**: `eql?` を使用するため、数値型の違いに注意（`1` と `1.0` は異なる）
4. **破壊的変更**: 元の配列を変更したい場合は別途 `replace` などを使う必要がある

```ruby
# eql? による比較の例
[1, 2, 3].difference([1.0])  # => [1, 2, 3] （1 と 1.0 は eql? で異なる）
[1, 2, 3] - [1.0]            # => [2, 3] （- は == を使用）
```

### 引数なしの挙動

```ruby
[1, 2, 3].difference  # => [1, 2, 3] （自身のコピーを返す）
```

引数がない場合は `dup` と同じようにコピーを返します。
