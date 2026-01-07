# Array#[]

- 日付: 2026-01-08
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-5B-5D

## 基本情報

```ruby
self[index] → object or nil
self[start, length] → object or nil
self[range] → object or nil
self[aseq] → object or nil
```

self から要素を返します。self を変更しません。

## メモ（daily より）

```ruby
# In brief:
# 要約:
a = [:foo, 'bar', 2]

# Single argument index: returns one element.
# 単一の引数 index: 1つの要素を返す。
a[0]     # => :foo        # Zero-based index.
a[-1]    # => 2           # Negative index counts backwards from end.
a[-4]    # => nil         # If index is out of range, returns nil.

# Arguments start and length: returns an array.
# 引数 start と length: 配列を返す。
a[1, 2]   # => ["bar", 2]
a[-2, 2]  # => ["bar", 2]   # Negative start counts backwards from end.
a[3, 2]   # => []           # If `start == self.size` and `length >= 0`, returns a new empty array.
a[1, -2]  # => nil          # If length is negative, returns nil.

# Single argument range: returns an array.
# 単一の引数 range: 配列を返す。
a[0..1]  # => [:foo, "bar"]
a[0..-2] # => [:foo, "bar"] # Negative range-begin counts backwards from end.
a[-2..2] # => ["bar", 2]    # Negative range-end counts backwards from end.
a[4..0]  # => nil           # If range.start is larger than the array size, returns nil.
```

## 深掘り・補足

### 重要なポイント

1. **多様なアクセス方法**
   - 単一インデックス: `array[index]`
   - 開始位置と長さ: `array[start, length]`
   - 範囲指定: `array[range]`
   - 4 つの異なるシグネチャを持つ柔軟なメソッド

2. **負のインデックス**
   - `-1` が最後の要素
   - `-2` が最後から 2 番目の要素
   - 配列の末尾からアクセスする際に便利

3. **範囲外アクセス**
   - インデックスが範囲外の場合、`nil` を返す
   - **エラーは発生しない**（安全だが、バグの原因になることも）

4. **破壊的でない操作**
   - 元の配列は変更されない
   - 新しい配列またはオブジェクトを返す

### ユースケース

```ruby
arr = [10, 20, 30, 40, 50]

# 最後の要素を取得
arr[-1]  # => 50

# 最初の3要素を取得
arr[0, 3]  # => [10, 20, 30]

# 範囲で中間部分を取得
arr[1..3]  # => [20, 30, 40]

# 末尾2要素を取得
arr[-2..-1]  # => [40, 50]
```

### 注意点

1. **範囲外アクセスの挙動**
   ```ruby
   arr = [1, 2, 3]
   arr[10]      # => nil（エラーにならない）
   arr.fetch(10) # => IndexError（エラーにしたい場合）
   ```

2. **length が負の場合**
   ```ruby
   arr[1, -2]  # => nil（length は 0 以上でなければならない）
   ```

3. **range の開始が範囲外の場合**
   ```ruby
   arr = [1, 2, 3]
   arr[10..12]  # => nil（開始が配列サイズより大きい）
   ```

4. **空配列が返るケース**
   ```ruby
   arr = [1, 2, 3]
   arr[3, 2]   # => []（start == size の場合）
   arr[1..0]   # => []（逆範囲の場合）
   ```

### パフォーマンスの考慮事項

- `[]` によるアクセスは O(1) の時間計算量
- 範囲指定の場合は O(n)（n は返される要素数）
- 配列は内部的に連続したメモリ領域を使用しているため、高速

### 関連メソッドとの比較

- **`slice`**: `[]` の別名（完全に同じ動作）
- **`at(index)`**: 単一インデックスのみ（`array[index]` と同等）
- **`fetch(index)`**: 範囲外アクセス時に例外を発生（デフォルト値も指定可能）
- **`first(n)`**: 最初の n 要素を取得
- **`last(n)`**: 最後の n 要素を取得
- **`values_at(*indexes)`**: 複数のインデックスを指定して要素を取得

```ruby
arr = [10, 20, 30, 40, 50]

arr[0]           # => 10
arr.at(0)        # => 10
arr.fetch(0)     # => 10

arr[10]          # => nil
arr.fetch(10)    # => IndexError
arr.fetch(10, 0) # => 0（デフォルト値）

arr.first(2)     # => [10, 20]
arr.last(2)      # => [40, 50]
arr.values_at(0, 2, 4)  # => [10, 30, 50]
```
