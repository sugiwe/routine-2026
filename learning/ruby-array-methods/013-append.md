# Array#append

- 日付: 2026-01-13
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-append

## 基本情報

```ruby
append(*objects) → self
```

配列の末尾に引数として与えられたオブジェクトを追加します。自身を返します。

## daily からの記録

### メモ

`append(*objects) → self`

### コード例・補足

```ruby
# Appends each argument in objects to self; returns self:
# 引数として与えられたオブジェクトの各要素を自身に追加します。自身を返します。
a = [:foo, 'bar', 2] # => [:foo, "bar", 2]
a.push(:baz, :bat)   # => [:foo, "bar", 2, :baz, :bat]

# Appends each argument as a single element, even if it is another array:
# 各引数を、たとえそれが別の配列であっても、単一の要素として追加します。
a = [:foo, 'bar', 2]               # => [:foo, "bar", 2]
a.push([:baz, :bat], [:bam, :bad]) # => [:foo, "bar", 2, [:baz, :bat], [:bam, :bad]]

# Related: see Methods for Assigning.

# Alias for: push
```

## 深掘り・補足

### 重要なポイント

1. **push のエイリアス**
   - `append` は `push` の完全なエイリアスです
   - 機能的には全く同じで、パフォーマンスにも差はありません
   - Ruby 2.5 で追加された比較的新しいメソッドです

2. **破壊的メソッド**
   - 元の配列を直接変更します（非破壊的版はありません）
   - 自身を返すため、メソッドチェーンが可能です

3. **可変長引数**
   - 一度に複数の要素を追加できます
   - `append(1, 2, 3)` のように複数渡せます

4. **配列を要素として追加**
   - 配列を引数に渡すと、その配列全体が 1 つの要素として追加されます
   - 配列の中身を展開して追加したい場合は `concat` や `+` を使います

### ユースケース

```ruby
# 基本的な使い方
fruits = ['apple', 'banana']
fruits.append('orange')
# => ['apple', 'banana', 'orange']

# 複数要素を一度に追加
fruits.append('grape', 'melon')
# => ['apple', 'banana', 'orange', 'grape', 'melon']

# メソッドチェーン
numbers = [1, 2, 3]
numbers.append(4, 5).append(6, 7)
# => [1, 2, 3, 4, 5, 6, 7]

# 配列を要素として追加（ネストした配列になる）
arr = [1, 2]
arr.append([3, 4])
# => [1, 2, [3, 4]]
```

### 関連メソッドとの比較

#### `append` vs `push`（完全に同じ）
```ruby
arr1 = [1, 2]
arr1.append(3, 4)  # => [1, 2, 3, 4]

arr2 = [1, 2]
arr2.push(3, 4)    # => [1, 2, 3, 4]
```

#### `append` vs `<<`（左シフト演算子）
```ruby
# << は一度に 1 つの要素しか追加できない
arr = [1, 2]
arr << 3 << 4      # => [1, 2, 3, 4]

# append は複数要素を一度に追加できる
arr = [1, 2]
arr.append(3, 4)   # => [1, 2, 3, 4]
```

#### `append` vs `concat`
```ruby
# append: 配列を要素として追加（ネストする）
arr1 = [1, 2]
arr1.append([3, 4])     # => [1, 2, [3, 4]]

# concat: 配列の中身を展開して追加（フラットにする）
arr2 = [1, 2]
arr2.concat([3, 4])     # => [1, 2, 3, 4]
```

#### `append` vs `+`
```ruby
# +: 非破壊的（新しい配列を返す）
arr1 = [1, 2]
arr2 = arr1 + [3, 4]
# arr1 => [1, 2]（変更されない）
# arr2 => [1, 2, 3, 4]

# append: 破壊的（元の配列を変更）
arr3 = [1, 2]
arr3.append(3, 4)
# arr3 => [1, 2, 3, 4]（変更される）
```

### 注意点

1. **破壊的な変更**
   ```ruby
   original = [1, 2, 3]
   same_ref = original
   original.append(4)

   # original と same_ref は同じオブジェクトを参照している
   puts original  # => [1, 2, 3, 4]
   puts same_ref  # => [1, 2, 3, 4]
   ```

2. **配列のネスト**
   ```ruby
   # 配列を展開せずにそのまま追加される
   arr = [1, 2]
   arr.append([3, 4], [5, 6])
   # => [1, 2, [3, 4], [5, 6]]

   # フラットにしたい場合は concat を使う
   arr = [1, 2]
   arr.concat([3, 4], [5, 6])
   # => [1, 2, 3, 4, 5, 6]
   ```

3. **nil も要素として追加される**
   ```ruby
   arr = [1, 2]
   arr.append(nil)
   # => [1, 2, nil]
   ```

### パフォーマンスの考慮事項

- `append` と `push` は同じ実装なのでパフォーマンスに差はありません
- 配列の末尾への追加は O(1) の操作です（償却時間計算量）
- 大量の要素を追加する場合、一度に複数渡す方が効率的です
  ```ruby
  # 非効率的
  1000.times { |i| arr.append(i) }

  # より効率的
  arr.append(*1..1000)
  ```

### なぜ append と push が両方存在するのか？

- **可読性の向上**: `append` の方が意図が明確で読みやすいと感じる人もいます
- **他言語からの移行**: JavaScript など他の言語では `push` が一般的ですが、Python では `append` を使うため、Python 経験者には馴染みやすい
- **Ruby の哲学**: Ruby は「同じことを複数の方法で書ける柔軟性」を重視しています

好みやチームの規約に応じて使い分けてください。どちらを使っても機能的には全く同じです。
