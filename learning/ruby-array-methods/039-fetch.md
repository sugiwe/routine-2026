# Array#fetch

- 日付: 2026-02-08
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-fetch
- daily 記録: [daily/2026-02-08.md](../../daily/2026-02-08.md)

## 基本情報

```ruby
fetch(index) → element
fetch(index, default_value) → element or default_value
fetch(index) {|index| ... } → element or block_return_value
```

指定されたインデックスの要素を返します。`index` は整数に変換可能なオブジェクトである必要があります。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# fetch(index) → element
# fetch(index, default_value) → element or default_value
# fetch(index) {|index| ... } → element or block_return_value

# Returns the element of self at offset index if index is in range; index must be an integer-convertible object.
# `index`が有効な範囲内であれば、その位置にある要素を返します。`index`には整数に変換可能なオブジェクトを指定してください。

# With the single argument index and no block, returns the element at offset index:
# 引数にインデックスのみを取り、ブロックがない場合、オフセットインデックスの位置にある要素を返します。
a = [:foo, 'bar', 2]
a.fetch(1)   # => "bar"
a.fetch(1.1) # => "bar"

# If index is negative, counts from the end of the array:
# インデックスが負の値の場合、配列の末尾から数えます。
a = [:foo, 'bar', 2]
a.fetch(-1) # => 2
a.fetch(-2) # => "bar"

# With arguments index and default_value (which may be any object) and no block, returns default_value if index is out-of-range:
# 引数 `index` および `default_value` (任意オブジェクト可) を指定し、ブロックを省略した場合、`index` が範囲外であれば `default_value` を返します。
a = [:foo, 'bar', 2]
a.fetch(1, nil)  # => "bar"
a.fetch(3, :foo) # => :foo

# With argument index and a block, returns the element at offset index if index is in range (and the block is not called); otherwise calls the block with index and returns its return value:
# 引数インデックスとブロックを使い、インデックスが範囲内であればオフセットインデックスにある要素を返します（ブロックは呼ばれません）。それ以外の場合、ブロックをインデックス付きで呼び出し、その戻り値を返します。
a = [:foo, 'bar', 2]
a.fetch(1) {|index| raise 'Cannot happen' } # => "bar"
a.fetch(50) {|index| "Value for #{index}" } # => "Value for 50"
```

## 深掘り・補足

### `[]` との決定的な違い

`fetch` と `[]` の最も重要な違いは、**範囲外のインデックスへのアクセス時の挙動**です：

```ruby
arr = ['a', 'b', 'c']

# [] は nil を返す
arr[100]        # => nil
arr[-100]       # => nil

# fetch は IndexError を発生させる
arr.fetch(100)  # IndexError: index 100 outside of array bounds: -3...3
arr.fetch(-100) # IndexError: index -100 outside of array bounds: -3...3
```

この違いにより、`fetch` は**意図しないバグを早期発見できる**という利点があります。

### なぜ fetch を使うべきか

#### 問題となるケース

```ruby
users = ['Alice', 'Bob', 'Charlie']
user_index = get_user_index()  # 何らかの処理で取得

# [] を使った場合
name = users[user_index]
puts "Hello, #{name}!"  # user_index が範囲外なら "Hello, !" になる

# fetch を使った場合
name = users.fetch(user_index)  # IndexError で問題を即座に検出
puts "Hello, #{name}!"
```

`[]` を使うと、nil が返されても処理が続行され、後でデバッグが困難になる可能性があります。

### デフォルト値の使い分け

#### パターン1：固定のデフォルト値

```ruby
colors = ['red', 'green', 'blue']

# 範囲外なら 'unknown' を返す
colors.fetch(5, 'unknown')  # => 'unknown'
```

#### パターン2：ブロックで動的に生成

```ruby
colors = ['red', 'green', 'blue']

# 範囲外なら、インデックス情報を含むメッセージを生成
colors.fetch(5) { |i| "Color at index #{i} is not defined" }
# => "Color at index 5 is not defined"
```

**重要**：ブロックは**範囲外のときだけ**評価されます。これにより、不要な計算を避けられます。

```ruby
# デフォルト値は常に評価される（非効率）
arr.fetch(0, expensive_computation())

# ブロックは必要なときだけ評価される（効率的）
arr.fetch(0) { expensive_computation() }
```

### 実践的なユースケース

#### 1. 設定値の取得

```ruby
config = ['development', 'localhost', 3000]

# 必須の設定値を取得（存在しないとエラー）
env = config.fetch(0)

# オプションの設定値を取得（デフォルト値あり）
timeout = config.fetch(5, 30)  # => 30
```

#### 2. メソッドの引数検証

```ruby
def process_item(items, index)
  # インデックスが有効か厳密にチェック
  item = items.fetch(index)
  # 処理...
rescue IndexError => e
  puts "Invalid index: #{index}"
end
```

#### 3. 安全なデータアクセス

```ruby
# CSV の行データなど、固定長でないデータを扱う場合
row = ['John', 'Doe', '30']

first_name = row.fetch(0, 'Unknown')  # => 'John'
last_name  = row.fetch(1, 'Unknown')  # => 'Doe'
age        = row.fetch(2, '0')        # => '30'
address    = row.fetch(3, 'N/A')      # => 'N/A'（存在しないのでデフォルト値）
```

### 注意点とベストプラクティス

1. **nil が有効な値の場合は注意**
   ```ruby
   arr = [1, nil, 3]

   # fetch は nil でも正常に返す
   arr.fetch(1)  # => nil

   # [] も nil を返すが、範囲外かどうか判別できない
   arr[1]        # => nil
   arr[5]        # => nil（どちらも nil）
   ```

2. **負のインデックスも使える**
   ```ruby
   arr = ['a', 'b', 'c']
   arr.fetch(-1)     # => 'c'（末尾）
   arr.fetch(-2)     # => 'b'
   arr.fetch(-10)    # IndexError
   ```

3. **整数に変換可能な値も受け付ける**
   ```ruby
   arr = ['a', 'b', 'c']
   arr.fetch(1.5)    # => 'b'（1.5 は 1 に変換される）
   arr.fetch('1')    # TypeError: no implicit conversion of String into Integer
   ```

### まとめ

- **`fetch` は安全性重視**：範囲外アクセスを検出したい場合に使う
- **`[]` は柔軟性重視**：nil を許容できる、または範囲チェックが不要な場合に使う
- **デフォルト値**：固定値なら第 2 引数、計算が必要ならブロックを使う
- **エラー処理**：`fetch` + `rescue IndexError` で明示的なエラーハンドリングが可能

`fetch` を適切に使うことで、より堅牢で保守しやすいコードを書くことができます。
