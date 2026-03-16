# Array#replace

- 日付: 2026-03-16
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-replace
- daily 記録: [daily/2026-03-16.md](../../daily/2026-03-16.md)

## 基本情報

```ruby
replace(other_array) → self
```

**概要**: `self`の要素を`other_array`の要素に置き換え、`self`を返します。`other_array`は配列に変換可能なオブジェクトである必要があります。

**エイリアス**: `initialize_copy`

**カテゴリ**: Methods for Assigning（要素の追加、置換、並べ替えを行うメソッド）

## ユーザーの学習記録（daily より）

### コード例

```ruby
# Replaces the elements of self with the elements of other_array, which must be an array-convertible object; returns self:
# `self`の要素を`other_array`の要素に置き換えます。`other_array`は配列に変換可能なオブジェクトである必要があります。`self`を返します。
a = ['a', 'b', 'c']   # => ["a", "b", "c"]
a.replace(['d', 'e']) # => ["d", "e"]

# Related: see Methods for Assigning.
# Alias for: initialize_copy
```

## 深掘り・補足

### 重要なポイント

#### 1. 破壊的メソッド

`replace`は**破壊的メソッド**であり、元の配列の内容を完全に置き換えます。

```ruby
a = [1, 2, 3]
a.replace([4, 5])
p a  # => [4, 5]（元の配列が変更される）
```

#### 2. `self`を返す

メソッドは`self`を返すため、**メソッドチェーン**が可能です。

```ruby
a = [1, 2, 3]
result = a.replace([4, 5]).map { |n| n * 2 }
p result  # => [8, 10]
p a       # => [4, 5]
```

#### 3. オブジェクトIDは変わらない

`replace`は配列の**内容**を置き換えますが、**オブジェクトID（object_id）は変わりません**。

```ruby
a = [1, 2, 3]
original_id = a.object_id

a.replace([4, 5])
p a.object_id == original_id  # => true（同じオブジェクト）
```

**比較**: 新しい配列を代入した場合

```ruby
a = [1, 2, 3]
original_id = a.object_id

a = [4, 5]  # 新しいオブジェクトを代入
p a.object_id == original_id  # => false（別のオブジェクト）
```

### ユースケース

#### ケース1: 参照を保ったまま内容を更新

複数の変数が同じ配列を参照している場合、`replace`を使うと**すべての参照先が更新されます**。

```ruby
a = [1, 2, 3]
b = a  # 同じオブジェクトを参照

a.replace([4, 5])

p a  # => [4, 5]
p b  # => [4, 5]（bも更新される）
```

**比較**: 代入の場合

```ruby
a = [1, 2, 3]
b = a

a = [4, 5]  # 新しいオブジェクトを代入

p a  # => [4, 5]
p b  # => [1, 2, 3]（bは変わらない）
```

#### ケース2: 配列の完全リセット

配列の内容を完全にクリアして新しい要素に置き換えたい場合。

```ruby
shopping_list = ['りんご', 'バナナ', 'みかん']
shopping_list.replace(['牛乳', 'パン'])
p shopping_list  # => ["牛乳", "パン"]
```

### 注意点

#### 1. 配列に変換可能なオブジェクトが必要

`other_array`は配列または`to_ary`メソッドを持つオブジェクトである必要があります。

```ruby
a = [1, 2, 3]

# ✅ 配列で置き換え
a.replace([4, 5])  # OK

# ❌ 配列に変換できないオブジェクト
a.replace(123)     # TypeError: no implicit conversion of Integer into Array
```

#### 2. 元の配列は完全に失われる

`replace`を実行すると、元の要素は**完全に失われます**。必要であれば事前にバックアップを取る必要があります。

```ruby
a = [1, 2, 3]
backup = a.dup  # バックアップを作成

a.replace([4, 5])
p a       # => [4, 5]
p backup  # => [1, 2, 3]（元の内容を保持）
```

### 関連メソッドとの比較

#### `clear` + `concat`

`replace`は、`clear`してから`concat`する処理と同等です。

```ruby
a = [1, 2, 3]

# replace を使う場合
a.replace([4, 5])

# clear + concat を使う場合（同じ結果）
a.clear
a.concat([4, 5])
```

#### `[]=` との違い

`[]=`は特定の範囲を置き換えますが、`replace`は**配列全体**を置き換えます。

```ruby
a = [1, 2, 3, 4, 5]

# []= で一部を置き換え
a[1..3] = [9, 9]
p a  # => [1, 9, 9, 5]

# replace で全体を置き換え
a.replace([7, 8])
p a  # => [7, 8]
```

### パフォーマンスの考慮事項

- `replace`は内部的に配列のメモリを再利用するため、新しい配列を作成するよりも**メモリ効率が良い**場合があります
- ただし、大量の要素を扱う場合は、`clear` + `concat`と同様のコストがかかります

### まとめ

**`replace`が適している場面**:
- オブジェクト参照を保ったまま内容を更新したい
- 配列全体を新しい内容に置き換えたい
- メモリ効率を考慮したい

**代替手段**:
- 新しい配列を代入: `a = [1, 2, 3]`（参照が変わる）
- `clear` + `concat`: `a.clear.concat([1, 2, 3])`（同等の処理）

### 関連メソッド

- `clear` - 配列の全要素を削除
- `concat` - 配列を連結
- `[]=` - 特定の範囲を置き換え
- `initialize_copy` - `replace`のエイリアス元
