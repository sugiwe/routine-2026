# Array#rindex

- 日付: 2026-03-20
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-rindex
- daily 記録: [daily/2026-03-20.md](../../daily/2026-03-20.md)

## 基本情報

```ruby
rindex(object) → integer or nil
rindex { |element| ... } → integer or nil
rindex → new_enumerator
```

**概要:**
与えられた条件を満たす**最後の**要素のインデックスを返します。`index` メソッドの逆向きバージョンです。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

（メモなし）

### コード例

```ruby
# Returns the index of the last element for which object == element.
# オブジェクトと一致する最後の要素のインデックスを返します。
# With argument object given, returns the index of the last such element found:
# 引数にオブジェクトが与えられた場合、その要素が最後に出現するインデックスを返します。
a = [:foo, 'bar', 2, 'bar']
a.rindex('bar') # => 3

# Returns nil if no such object found.
# 該当するオブジェクトが見つからない場合は、nilを返します。
# With a block given, calls the block with each successive element; returns the index of the last element for which the block returns a truthy value:
# ブロックが与えられた場合、各要素に対して順番にブロックを呼び出します。ブロックが真となる値を返した最後の要素のインデックスを返します。
a = [:foo, 'bar', 2, 'bar']
a.rindex {|element| element == 'bar' } # => 3

# Returns nil if the block never returns a truthy value.
# When neither an argument nor a block is given, returns a new Enumerator.
# Related: see Methods for Querying.
```

## 深掘り・補足

### 重要なポイント

1. **「最後の」要素を探す**
   - `index` が最初の一致を返すのに対し、`rindex` は最後の一致を返す
   - 配列の末尾から逆向きに検索する動作

2. **3つの使い方**
   - **引数あり**: `rindex(object)` - オブジェクトと `==` で一致する最後の要素
   - **ブロックあり**: `rindex { |element| ... }` - ブロックが真を返す最後の要素
   - **引数なし**: `rindex` - Enumerator を返す

3. **見つからない場合**
   - 一致する要素がない場合は `nil` を返す

### ユースケース

#### 1. 重複する値の最後の位置を探す

```ruby
tags = ['ruby', 'rails', 'ruby', 'gem', 'ruby']
tags.rindex('ruby')  # => 4（最後の 'ruby' の位置）
```

#### 2. 条件に合う最後の要素を探す

```ruby
numbers = [1, 5, 10, 15, 20, 25]
numbers.rindex { |n| n < 20 }  # => 3（15 のインデックス）
```

#### 3. 配列の末尾から特定の条件で検索

```ruby
logs = [
  { level: 'info', message: 'Started' },
  { level: 'error', message: 'Failed' },
  { level: 'info', message: 'Retrying' },
  { level: 'error', message: 'Timeout' }
]

# 最後のエラーログの位置
last_error_index = logs.rindex { |log| log[:level] == 'error' }  # => 3
```

### index との比較

```ruby
fruits = ['apple', 'banana', 'apple', 'cherry', 'apple']

# 最初の apple
fruits.index('apple')   # => 0

# 最後の apple
fruits.rindex('apple')  # => 4
```

### 注意点

1. **検索方向**
   - `rindex` という名前ですが、実際の検索は**配列全体を評価**します
   - ただし、論理的には「末尾から最初に見つかる要素」を返すと考えられます

2. **パフォーマンス**
   - ブロックを使う場合、全要素を評価する可能性がある
   - 大きな配列で頻繁に使う場合は注意

3. **nil との区別**
   - 見つからない場合は `nil` を返す
   - インデックス 0 と `nil` を区別する必要がある

```ruby
arr = ['a', 'b', 'c', 'b']
value = 'b'
result = arr.rindex(value)
if result
  puts "Found at index #{result}"  # => Found at index 3
else
  puts "'#{value}' not found"
end

# 見つからない場合
arr = ['a', 'b', 'c']
value = 'd'
result = arr.rindex(value)
if result
  puts "Found at index #{result}"
else
  puts "'#{value}' not found"  # => 'd' not found
end
```

### 関連メソッド

- `index` - 最初の一致を探す（順方向）
- `find_index` - `index` のエイリアス
- `rfind` - 条件に一致する最後の要素を返す（Ruby 3.4+）。`rindex` はインデックスを返すが、`rfind` は要素自体を返す
- `reverse.find` - 配列を反転してから `find` を呼ぶ方法（非効率）
- `arr[arr.rindex(...)]` - `rindex` で位置を取得してから要素にアクセス

### 実践的な例

```ruby
# ログファイルから最後のエラーの位置を見つける
log_lines = ['INFO: Started', 'ERROR: Connection failed', 'INFO: Retry', 'ERROR: Timeout']
last_error_line = log_lines.rindex { |line| line.start_with?('ERROR') }
# => 3

# 配列の末尾から特定の値より小さい最後の要素
numbers = [1, 5, 10, 15, 20, 15, 10, 5]
numbers.rindex { |n| n < 10 }  # => 7（最後の 5 のインデックス）
```

`rindex` は、配列の末尾から検索したい場合や、重複する値の最後の位置を知りたい場合に非常に便利なメソッドです。
