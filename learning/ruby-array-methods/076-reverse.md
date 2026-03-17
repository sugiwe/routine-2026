# Array#reverse

- 日付: 2026-03-17
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-reverse
- daily 記録: [daily/2026-03-17.md](../../daily/2026-03-17.md)

## 基本情報

```ruby
reverse → new_array
```

配列の要素を逆順にした新しい配列を返します。元の配列は変更されません（非破壊的メソッド）。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

（ユーザーメモなし）

### コード例

```ruby
# Returns a new array containing the elements of self in reverse order:
# 自身の要素を逆順にした新しい配列を返します。
[0, 1, 2].reverse # => [2, 1, 0]

# Related: see Methods for Combining.
```

## 深掘り・補足

### 動作の詳細

`reverse` は配列の要素順序を逆転させた**新しい配列**を返します。元の配列はそのまま保持されます。

```ruby
arr = [1, 2, 3, 4, 5, 6]
reversed = arr.reverse

reversed  # => [6, 5, 4, 3, 2, 1]
arr       # => [1, 2, 3, 4, 5, 6]（元の配列は変更なし）
```

### 実践的な使用例

#### 1. データの逆順表示

```ruby
# 最新の投稿を上に表示
posts = ["古い投稿", "少し新しい投稿", "最新の投稿"]
posts.reverse  # => ["最新の投稿", "少し新しい投稿", "古い投稿"]
```

#### 2. アルゴリズムでの利用

```ruby
# 回文（palindrome）判定
word = "racecar"
word.chars == word.chars.reverse  # => true

word = "hello"
word.chars == word.chars.reverse  # => false
```

#### 3. スタック構造の反転

```ruby
stack = [1, 2, 3, 4]
stack.reverse  # => [4, 3, 2, 1]
# 後入れ先出し（LIFO）から先入れ先出し（FIFO）への変換
```

### 関連メソッドとの比較

| メソッド | 破壊性 | 戻り値 | 用途 |
|---------|--------|--------|------|
| `reverse` | 非破壊 | 新しい配列 | 元の配列を保持したい場合 |
| `reverse!` | 破壊的 | 自身（変更後） | メモリ効率重視、元の配列が不要 |
| `reverse_each` | - | Enumerator | 逆順でイテレーション |

#### reverse! との使い分け

```ruby
# 非破壊版（reverse）
original = [1, 2, 3]
reversed = original.reverse
# original: [1, 2, 3] - 保持される
# reversed: [3, 2, 1] - 新しい配列

# 破壊版（reverse!）
arr = [1, 2, 3]
arr.reverse!
# arr: [3, 2, 1] - 元の配列が変更される
```

#### reverse_each との使い分け

```ruby
# reverse を使う場合（配列が必要）
[1, 2, 3].reverse.each { |n| puts n }
# => 3, 2, 1（新しい配列を生成してからイテレート）

# reverse_each を使う場合（配列が不要）
[1, 2, 3].reverse_each { |n| puts n }
# => 3, 2, 1（配列を生成せず直接イテレート、効率的）
```

### パフォーマンスの考慮事項

- **メモリ**: `reverse` は新しい配列を生成するため、元の配列と同じサイズのメモリを消費
- **速度**: O(n) の時間計算量（要素数に比例）
- **効率化**: 逆順でイテレートするだけなら `reverse_each` を使う方が効率的

```ruby
# 非効率（新しい配列を生成）
large_array.reverse.each { |item| process(item) }

# 効率的（配列生成なし）
large_array.reverse_each { |item| process(item) }
```

### 空配列や単一要素の場合

```ruby
[].reverse      # => []
[42].reverse    # => [42]
```

### 注意点

1. **元の配列は変更されない**: 変更が必要なら `reverse!` を使用
2. **新しいオブジェクト**: 新しい配列が生成されるため、メモリ使用に注意
3. **ネストした配列**: 要素の順序のみ反転、要素自体は変更されない

```ruby
nested = [[1, 2], [3, 4], [5, 6]]
nested.reverse  # => [[5, 6], [3, 4], [1, 2]]
# 各サブ配列の内部は反転されない
```

### よくあるユースケース

- ログファイルの逆順表示（最新から表示）
- タイムラインの降順ソート後の表示
- 履歴データの新しい順表示
- アルゴリズム問題での配列反転
