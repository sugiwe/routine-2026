# Array#fetch_values

- 日付: 2026-02-09
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-fetch_values
- daily 記録: [daily/2026-02-09.md](../../daily/2026-02-09.md)

## 基本情報

```ruby
fetch_values(*indexes) → new_array
fetch_values(*indexes) { |index| ... } → new_array
```

指定された複数のインデックス位置にある要素を取得し、新しい配列として返すメソッドです。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

どういう時に使うメソッドなんだろう？

### コード例

```ruby
# fetch_values(*indexes) → new_array
# fetch_values(*indexes) { |index| ... } → new_array

# With no block given, returns a new array containing the elements of self at the offsets specified by indexes. Each of the indexes must be an integer-convertible object:
# ブロックが指定されていない場合、`self` の要素を `indexes` で指定されたオフセットに持つ新しい配列を返します。`indexes` の各要素は、整数に変換可能なオブジェクトである必要があります。
a = [:foo, :bar, :baz]
a.fetch_values(2, 0)   # => [:baz, :foo]
a.fetch_values(2.1, 0) # => [:baz, :foo]
a.fetch_values         # => []

# For a negative index, counts backwards from the end of the array:
# 負のインデックスの場合、配列の末尾から逆方向にカウントします。
a.fetch_values(-2, -1) # [:bar, :baz]

# When no block is given, raises an exception if any index is out of range.
# ブロックが指定されていない場合、いずれかのインデックスが範囲外であると例外を発生させます。

# With a block given, for each index:
# - If the index is in range, uses an element of self (as above).
# - Otherwise, calls the block with the index and uses the block's return value.
# ブロックが与えられた場合、各インデックスについて、以下のようになります。
#  - インデックスが範囲内であれば、`self` の要素（上記参照）を使用します。
#  - それ以外の場合は、インデックスを引数としてブロックを呼び出し、その戻り値を使用します。

# Example:
a = [:foo, :bar, :baz]
a.fetch_values(1, 0, 42, 777) { |index| index.to_s }
# => [:bar, :foo, "42", "777"]
```

## 深掘り・補足

### 重要なポイント

1. **複数のインデックスを一度に取得**
   - `fetch_values` は複数のインデックスを指定して、それぞれに対応する要素を一度に取得できます
   - 引数に指定した順序で要素が返されるため、並び替えも可能です

2. **範囲外アクセスの厳格なハンドリング**
   - ブロックが指定されていない場合、インデックスが範囲外だと `IndexError` 例外が発生します
   - これは `values_at` との大きな違いです（`values_at` は `nil` を返す）

3. **ブロックによるデフォルト値の提供**
   - ブロックを渡すことで、範囲外のインデックスに対してデフォルト値を動的に生成できます
   - インデックス値がブロックに渡されるため、柔軟な処理が可能です

### ユースケース

#### 1. 特定の位置の要素を取得して並び替え

```ruby
names = ['Alice', 'Bob', 'Charlie', 'David']
names.fetch_values(3, 1, 0)
# => ["David", "Bob", "Alice"]
```

#### 2. 範囲外アクセスの検出

```ruby
data = [10, 20, 30]
begin
  data.fetch_values(0, 1, 5)  # IndexError が発生
rescue IndexError => e
  puts "Invalid index detected: #{e.message}"
end
```

#### 3. ブロックによるフォールバック値の提供

```ruby
items = ['apple', 'banana', 'cherry']
result = items.fetch_values(1, 0, 10, 20) { |i| "index_#{i}_not_found" }
# => ["banana", "apple", "index_10_not_found", "index_20_not_found"]
```

### 注意点

1. **`values_at` との違い**
   - `values_at`: 範囲外のインデックスには `nil` を返す（安全だが曖昧）
   - `fetch_values`: 範囲外のインデックスには例外を発生（厳格だが明確）

   ```ruby
   arr = [1, 2, 3]
   arr.values_at(0, 5)      # => [1, nil]
   arr.fetch_values(0, 5)   # IndexError: index 5 out of array
   ```

2. **パフォーマンスの考慮事項**
   - 複数の要素を一度に取得するため、ループで個別に `fetch` を呼ぶよりも効率的
   - ただし、ブロックを使用する場合、範囲外のインデックスごとにブロックが評価される

3. **整数変換可能なオブジェクト**
   - インデックスとして整数変換可能なオブジェクト（`2.1` など）を渡せます
   - 内部で `to_int` が呼ばれて整数に変換されます

### 関連メソッドとの比較

| メソッド | 範囲外の扱い | 複数取得 | デフォルト値 |
|---------|------------|---------|-------------|
| `fetch` | 例外 or ブロック | ✗（単一のみ） | ✓（ブロック） |
| `fetch_values` | 例外 or ブロック | ✓ | ✓（ブロック） |
| `values_at` | `nil` を返す | ✓ | ✗ |
| `[]` | `nil` を返す | ✗（単一のみ） | ✗ |

### どういう時に使うメソッド？（第一印象への回答）

`fetch_values` は以下のような場合に有用です：

1. **複数の特定位置の要素を一度に取得したい**
   - しかも、インデックスが範囲外の場合にエラーとして検出したい

2. **データの部分的な再配置が必要**
   - 元の配列から特定の要素を任意の順序で取り出したい

3. **範囲外アクセスを厳密にチェックしたい**
   - `values_at` のように `nil` を返すのではなく、明示的にエラーにしたい場合

4. **フォールバック値をインデックスに基づいて動的に生成したい**
   - ブロックを使って、インデックスごとに異なるデフォルト値を返す
