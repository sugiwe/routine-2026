# Array#\*

- 日付: 2026-01-02
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-2A

## 基本情報

`self * n → new_array`
`self * string_separator → new_string`

引数が整数の場合は配列の繰り返し、文字列の場合は結合（join と同等）を行います。

## メモ（daily より）

```ruby
# When non-negative integer argument n is given, returns a new array built by concatenating n copies of self:
# 非負の整数引数 n が与えられた場合、self を n 個複製して連結した新しい配列を返します:
a = ['x', 'y']
a * 3 # => ["x", "y", "x", "y", "x", "y"]

# When string argument string_separator is given, equivalent to self.join(string_separator):
# 文字列引数 string_separator が指定された場合、self.join(string_separator) と同等です:
[0, [0, 1], {foo: 0}] * ', ' # => "0, 0, 1, {foo: 0}"
```

## 深掘り・補足

### 2 つの異なる挙動

このメソッドは引数の型によって全く異なる動作をします：

#### 1. 整数を渡した場合（配列の繰り返し）

```ruby
# ダミーデータの生成に便利
initial_values = [0] * 5  # => [0, 0, 0, 0, 0]
grid_row = [nil] * 3      # => [nil, nil, nil]
```

**注意点**: 同じオブジェクトが参照されるため、ミュータブルなオブジェクトには注意が必要です

```ruby
# 危険な例
rows = [[]] * 3
rows[0] << 1
rows  # => [[1], [1], [1]]  # 全て同じ配列オブジェクト！

# 正しい方法
rows = Array.new(3) { [] }
rows[0] << 1
rows  # => [[1], [], []]
```

#### 2. 文字列を渡した場合（join のエイリアス）

```ruby
# CSV風の文字列生成
['apple', 'banana', 'cherry'] * ','  # => "apple,banana,cherry"

# パスの生成
['usr', 'local', 'bin'] * '/'  # => "usr/local/bin"
```

### ユースケース

- テストデータの生成
- 初期値を持つ配列の作成
- 配列を区切り文字で結合した文字列の生成

### `join` との違い

文字列を渡した場合は `join` と同等ですが、`join` の方が意図が明確です：

```ruby
# 両方とも同じ結果
[1, 2, 3] * '-'      # => "1-2-3"
[1, 2, 3].join('-')  # => "1-2-3"  # こちらの方が読みやすい
```
