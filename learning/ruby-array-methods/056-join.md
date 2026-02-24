# Array#join

- 日付: 2026-02-25
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-join
- daily 記録: [daily/2026-02-25.md](../../daily/2026-02-25.md)

## 基本情報

```ruby
join(separator = $,) → string
```

配列の要素を文字列に変換し、指定されたセパレータで結合して新しい文字列を返します。

各要素 `element` に対して：
- `element` が配列の場合、`element.join(separator)` を用いて再帰的に変換
- それ以外の場合は、`element.to_s` を使用して変換

## ユーザーの学習記録（daily より）

### コード例

```ruby
# Returns the new string formed by joining the converted elements of self; for each element `element`:
# selfの変換された各要素を結合して新しい文字列を返します。
# - Converts recursively using `element.join(separator)` if `element` is a `kind_of?(Array)`.
# - `element`が配列の場合、`element.join(separator)` を用いて再帰的に変換します。
# - Otherwise, converts using `element.to_s`.
# - それ以外の場合は、`element.to_s` を使用して変換されます。

# With no argument given, joins using the output field separator, `$,`:
# 引数を指定しない場合、出力フィールドセパレータである「$,」を使って結合します。
a = [:foo, 'bar', 2]
$, # => nil
a.join # => "foobar2"

# With string argument `separator` given, joins using that separator:
# 文字列引数`separator`を指定すると、そのセパレータを使って結合します。
a = [:foo, 'bar', 2]
a.join("\n") # => "foo\nbar\n2"

# Joins recursively for nested arrays:
# ネストされた配列を再帰的に結合します。
a = [:foo, [:bar, [:baz, :bat]]]
a.join # => "foobarbazbat"
```

## 深掘り・補足

### 重要なポイント

#### 1. デフォルト引数 `$,`（出力フィールドセパレータ）

`$,` はグローバル変数で、通常は `nil` です。引数を省略した場合：
- `$,` が `nil` → セパレータなしで結合（要素が直接つながる）
- `$,` に値を設定 → その値をセパレータとして使用

```ruby
$, = " | "
[:a, :b, :c].join  # => "a | b | c"
$, = nil           # 元に戻す
```

**注意**: `$,` はグローバル変数なので、変更するとプログラム全体に影響します。通常は明示的にセパレータを指定する方が安全です。

#### 2. 再帰的な処理

ネストされた配列も自動的に平坦化されて結合されます：

```ruby
# 深くネストされた配列も再帰的に処理
nested = [1, [2, [3, [4, 5]]]]
nested.join("-")  # => "1-2-3-4-5"

# 混在した構造も処理可能
mixed = ["hello", [:world, [:from, "Ruby"]]]
mixed.join(" ")  # => "hello world from Ruby"
```

これは `flatten.map(&:to_s).join(separator)` と似た結果になりますが、`join` の方がパフォーマンスが良いです。

#### 3. 型変換の仕組み

各要素は自動的に文字列に変換されます：

```ruby
# 数値
[1, 2, 3].join(", ")  # => "1, 2, 3"

# シンボル
[:foo, :bar].join(" ")  # => "foo bar"

# nil
[1, nil, 3].join(", ")  # => "1, , 3"  (nilは空文字列になる)

# カスタムオブジェクト（to_s が呼ばれる）
class Person
  def initialize(name)
    @name = name
  end

  def to_s
    @name
  end
end

[Person.new("Alice"), Person.new("Bob")].join(" & ")
# => "Alice & Bob"
```

### 実践的なユースケース

#### CSV 形式の生成

```ruby
headers = ["Name", "Age", "City"]
headers.join(",")  # => "Name,Age,City"

row = ["Alice", 30, "Tokyo"]
row.join(",")  # => "Alice,30,Tokyo"
```

#### パスの結合

```ruby
# ただし File.join の方が推奨
parts = ["home", "user", "documents", "file.txt"]
parts.join("/")  # => "home/user/documents/file.txt"
```

#### HTML/テキストの生成

```ruby
items = ["Apple", "Banana", "Orange"]
"<ul><li>#{items.join("</li><li>")}</li></ul>"
# => "<ul><li>Apple</li><li>Banana</li><li>Orange</li></ul>"

# 改行区切りのテキスト
lines = ["First line", "Second line", "Third line"]
lines.join("\n")
# => "First line\nSecond line\nThird line"
```

### パフォーマンスの考慮事項

`join` は内部的に最適化されており、文字列連結の中で最も効率的な方法の一つです：

```ruby
# ❌ 遅い：繰り返しの文字列連結
result = ""
array.each { |item| result += item.to_s + ", " }

# ✅ 速い：join を使用
result = array.join(", ")

# ✅ 速い：配列に蓄積してから join
parts = []
array.each { |item| parts << item.to_s }
result = parts.join(", ")
```

### 注意点・落とし穴

#### 1. `nil` の扱い

`nil` は空文字列として扱われるため、区別できません：

```ruby
[1, nil, 3].join(",")  # => "1,,3"
[1, "", 3].join(",")   # => "1,,3"  # 同じ結果
```

#### 2. セパレータは文字列のみ

セパレータは文字列でなければなりません。他の型を渡すとエラーになります：

```ruby
[1, 2, 3].join(0)  # TypeError: no implicit conversion of Integer into String
```

#### 3. 大きな配列でのメモリ使用

非常に大きな配列を `join` する場合、結果の文字列がメモリを大量に消費する可能性があります。その場合は、必要に応じてストリーミング処理を検討してください。

### 関連メソッドとの比較

- **`Array#*`**: 文字列を掛けると join と同じ結果（ただし読みにくい）
  ```ruby
  [1, 2, 3] * ", "  # => "1, 2, 3"  (join と同じ)
  ```

- **`Array#flatten`**: 配列を平坦化するが、文字列には変換しない
  ```ruby
  [[1, 2], [3, 4]].flatten  # => [1, 2, 3, 4]
  [[1, 2], [3, 4]].join     # => "1234"
  ```

- **`String#split`**: join の逆操作
  ```ruby
  "a,b,c".split(",")    # => ["a", "b", "c"]
  ["a", "b", "c"].join(",")  # => "a,b,c"
  ```

### まとめ

`Array#join` は：
- 配列を文字列に変換する最も効率的な方法
- 再帰的にネストされた配列を処理
- セパレータを柔軟に指定可能
- パフォーマンスに優れている

文字列の生成、CSV/TSV の作成、テキスト整形など、幅広い場面で活躍する基本的かつ重要なメソッドです。
