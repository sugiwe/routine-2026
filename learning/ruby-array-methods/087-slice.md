# Array#slice

- 日付: 2026-03-28
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-slice
- daily 記録: [daily/2026-03-28.md](../../daily/2026-03-28.md)

## 基本情報

`slice` は配列から要素を取り出すメソッドです。`[]` のエイリアスとして機能し、完全に同じ動作をします。

```ruby
slice(index) → obj or nil
slice(start, length) → new_array or nil
slice(range) → new_array or nil
slice(aseq) → new_array
```

公式ドキュメントでは「`slice` メソッドは `Array#[]` と同一の方法で機能する」と説明されています。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

（メモなし）

### コード例

```ruby
# Returns elements from self; does not modify self.
# 自身から要素を返します。自身を変更することはありません。

# In brief:
# 概要:
# ※エイリアス`[]`の方が一般的のため例は`[]`を多めに使っていますが、全て`slice`で書き換え可能

a = [:foo, 'bar', 2]

# Single argument index: returns one element.
# 単一のインデックス引数: 1つの要素を返します。
a[0]        # => :foo       # Zero-based index.
a.slice(0)  # => :foo       # 同じ結果
a[-1]       # => 2          # Negative index counts backwards from end.
                            # 負のインデックスは末尾から逆順にカウントします。

# Arguments start and length: returns an array.
# 引数 start と length: 配列を返します。
a[1, 2]        # => ["bar", 2]
a.slice(1, 2)  # => ["bar", 2]  # 同じ結果
a[-2, 2]       # => ["bar", 2]  # Negative start counts backwards from end.
                                # 負の start は末尾から逆順にカウントします。

# Single argument range: returns an array.
# 単一の Range 引数: 配列を返します。
a[0..1]        # => [:foo, "bar"]
a.slice(0..1)  # => [:foo, "bar"]  # 同じ結果
a[0..-2]       # => [:foo, "bar"]  # Negative range-begin counts backwards from end.
                                   # 負の range-begin は末尾から逆順にカウントします。
a[-2..2]       # => ["bar", 2]     # Negative range-end counts backwards from end.
                                   # 負の range-end は末尾から逆順にカウントします。
```

## 深掘り・補足

### `slice` と `[]` の関係

`slice` は `[]` のエイリアスです。実務では `[]` の方が圧倒的によく使われますが、以下の理由で `slice` という名前も存在します：

- **意図の明示**: 「配列をスライスする」という意図が明確になる
- **メソッドチェーン**: `.slice(...)` の形でメソッドチェーンに組み込みやすい
- **可読性**: コードレビューで「何をしているか」が分かりやすい

```ruby
# 実務では [] が多い
arr[0]
arr[1, 3]

# でも slice も使える（同じ意味）
arr.slice(0)
arr.slice(1, 3)
```

### 引数パターンの詳細

#### 1. 単一インデックス

```ruby
a = [:foo, 'bar', 2]
a[0]     # => :foo          # 0から始まるインデックス
a[2]     # => 2             # 3番目の要素
a[-1]    # => 2             # 末尾から1番目
a[-2]    # => "bar"         # 末尾から2番目
a[10]    # => nil           # 範囲外は nil
```

#### 2. start + length 形式

```ruby
a = [:foo, 'bar', 2]
a[0, 2]  # => [:foo, "bar"] # インデックス0から2要素
a[1, 2]  # => ["bar", 2]    # インデックス1から2要素
a[0, 4]  # => [:foo, "bar", 2]  # 範囲外まで指定しても末尾まで
a[3, 1]  # => []            # 配列サイズと同じ開始位置は空配列
a[1, -1] # => nil           # 負の length は nil
```

#### 3. Range 形式

```ruby
a = [:foo, 'bar', 2]
a[0..1]  # => [:foo, "bar"] # インデックス0～1
a[1..2]  # => ["bar", 2]    # インデックス1～2
a[0..-1] # => [:foo, "bar", 2]  # 0から末尾まで
a[0..-2] # => [:foo, "bar"] # 0から末尾の1つ前まで
a[-2..2] # => ["bar", 2]    # 負のインデックスも使える
a[4..1]  # => nil           # 開始が範囲外は nil
```

#### 4. ArithmeticSequence（Ruby 2.6+）

```ruby
a = ['--', 'data1', '--', 'data2', '--', 'data3']
a[(1..).step(2)]  # => ["data1", "data2", "data3"]  # 1から2つ飛ばし

# Range と違い、範囲外は RangeError
a[(1..11).step(2)]  # RangeError (((1..11).step(2)) out of range)
a[(7..).step(2)]    # RangeError (((7..).step(2)) out of range)
```

### 重要なポイント

1. **非破壊的メソッド**: `slice` は元の配列を変更しません
   ```ruby
   a = [1, 2, 3]
   a.slice(0, 2)  # => [1, 2]
   a              # => [1, 2, 3]  # 変更されない
   ```

2. **破壊的版は `slice!`**: 元の配列を変更したい場合は `slice!` を使う
   ```ruby
   a = [1, 2, 3]
   a.slice!(0, 2)  # => [1, 2]
   a               # => [3]  # 変更される
   ```

3. **範囲外アクセスは nil**: インデックスが範囲外の場合は `nil` を返す
   ```ruby
   [1, 2, 3][10]  # => nil
   ```

### 関連メソッドとの比較

#### `fetch` との違い

`fetch` は範囲外アクセス時にエラーを発生させるか、デフォルト値を返します。

```ruby
a = [1, 2, 3]
a[10]          # => nil
a.fetch(10)    # IndexError: index 10 outside of array bounds
a.fetch(10, 0) # => 0  # デフォルト値
```

#### `at` との違い

`at` は単一要素の取得のみに特化しています。

```ruby
a = [1, 2, 3]
a.at(0)     # => 1
a.at(1, 2)  # ArgumentError (wrong number of arguments)
```

#### `first` / `last` との違い

`first` と `last` は先頭/末尾に特化した専門メソッドです。

```ruby
a = [1, 2, 3]
a.first     # => 1
a.first(2)  # => [1, 2]
a.last      # => 3
a.last(2)   # => [2, 3]
```

### 実践的なユースケース

#### 1. 配列の一部を取得

```ruby
# 最初の3要素
arr[0, 3]
arr[0..2]

# 最後の3要素
arr[-3..]
arr[-3, 3]
```

#### 2. 配列を分割

```ruby
arr = [1, 2, 3, 4, 5]
first_half = arr[0, arr.size / 2]   # => [1, 2]
second_half = arr[arr.size / 2..-1] # => [3, 4, 5]
```

#### 3. 安全なアクセス

```ruby
# nil を避けたい場合は fetch
value = arr.fetch(index, default_value)

# nil でも良い場合は []
value = arr[index]
```

### パフォーマンスの考慮事項

- `slice` / `[]` は O(1) または O(n) の計算量
  - 単一要素のアクセス: O(1)
  - 範囲アクセス: O(n)（n は取得する要素数）
- 範囲外チェックは内部で行われるため、安全に使える

### まとめ

`slice` は配列から要素を取り出す基本的なメソッドです。`[]` のエイリアスとして機能し、以下の特徴があります：

- **非破壊的**: 元の配列を変更しない
- **柔軟な引数**: インデックス、start+length、Range、ArithmeticSequence に対応
- **安全**: 範囲外アクセスは `nil` を返す
- **実務では `[]` が主流**: でも `slice` も同じ意味で使える

実務では `[]` の方がよく使われますが、`slice` という名前で明示的にスライス操作であることを示すこともできます。
