# Array#size

- 日付: 2026-03-27
- URL: https://docs.ruby-lang.org/en/4.0/Array.html
- daily 記録: [daily/2026-03-27.md](../../daily/2026-03-27.md)

## 基本情報

```ruby
array.size → Integer
```

配列に含まれる要素の個数を返します。

`size` は `length` のエイリアス（別名）であり、完全に同じ機能を提供します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

（メモなし）

### コード例

```ruby
# Returns the count of elements in self:
# 自身に含まれる要素数を返します：
[0, 1, 2].size # => 3
[].size        # => 0

# Related: see Methods for Querying.
# Alias for: length
```

## 深掘り・補足

### lengthとの関係

`size` と `length` は完全に同一のメソッドです。どちらを使っても結果は同じですが、以下のような使い分けの傾向があります：

```ruby
browsers = ['Chrome', 'Firefox', 'Safari']
browsers.size    # => 3
browsers.length  # => 3  # 完全に同じ
```

一般的には：
- **`size`**：より簡潔で読みやすい（3文字少ない）
- **`length`**：他のプログラミング言語との親和性が高い（JavaScript、Python等でも`length`を使用）

Rubyコミュニティでは両方が広く使われており、どちらを選んでも問題ありません。

### パフォーマンス特性

配列は内部的に要素数を常に追跡しているため、`size` メソッドは **O(1) の時間計算量**で実行されます：

```ruby
# 要素が100万個あっても即座に返る
huge_array = Array.new(1_000_000, 0)
huge_array.size  # => 1000000  # 瞬時に結果が返る
```

全要素を走査する必要がないため、配列のサイズに関わらず高速です。

### 関連メソッドとの違い

#### `count` との違い

`count` は条件を指定できる点が異なります：

```ruby
numbers = [1, 2, 3, 4, 5, 6]

# 要素数を取得
numbers.size           # => 6
numbers.length         # => 6

# 条件に一致する要素数を取得
numbers.count          # => 6（引数なしの場合はsizeと同じ）
numbers.count(3)       # => 1（値が3の要素の数）
numbers.count(&:even?) # => 3（偶数の要素の数）
```

**使い分け**：
- 単純に全要素数を知りたい場合 → `size` または `length`（高速）
- 条件に一致する要素数を知りたい場合 → `count`（O(n)の走査が発生）

#### `empty?` との違い

`empty?` は配列が空かどうかを真偽値で返します：

```ruby
[].size       # => 0
[].empty?     # => true

[1, 2, 3].size    # => 3
[1, 2, 3].empty?  # => false
```

**使い分け**：
- 要素数そのものが必要 → `size`
- 空かどうかだけ知りたい → `empty?`（意図がより明確）

```ruby
# 読みやすさの比較
if array.size == 0      # OK
if array.size.zero?     # OK
if array.empty?         # ベスト（意図が最も明確）

if array.size > 0       # OK
if !array.empty?        # ベスト
```

### 実践的なユースケース

#### 1. 配列が空でないことを確認

```ruby
users = User.where(active: true).to_a

if users.size > 0
  puts "Active users: #{users.size}"
else
  puts "No active users"
end

# または
puts "Active users: #{users.size}" unless users.empty?
```

#### 2. ページネーション

```ruby
items_per_page = 20
total_items = products.size
total_pages = (total_items / items_per_page.to_f).ceil

puts "Showing page 1 of #{total_pages} (#{total_items} items total)"
```

#### 3. バリデーション

```ruby
def validate_team_size(members)
  if members.size < 3
    raise "Team must have at least 3 members"
  elsif members.size > 10
    raise "Team cannot have more than 10 members"
  end
end
```

#### 4. プログレスバーの表示

```ruby
files.each_with_index do |file, index|
  process(file)
  puts "Progress: #{index + 1}/#{files.size}"
end
```

### 注意点

#### nilとの違い

配列が `nil` の場合、`size` を呼ぶとエラーになります：

```ruby
array = nil
array.size  # NoMethodError: undefined method `size' for nil:NilClass

# 安全な書き方
array&.size           # => nil（safe navigation operator）
array.to_a.size       # => 0（nilを空配列に変換）
(array || []).size    # => 0
```

#### Hashとの違い

`Hash` にも `size` メソッドがありますが、キーと値のペアの数を返します：

```ruby
hash = { name: "Alice", age: 30, city: "Tokyo" }
hash.size  # => 3（キーと値のペアが3つ）
```

### 関連メソッド

- **`length`**：`size` の元のメソッド（完全に同じ）
- **`count`**：条件に一致する要素数を返す（柔軟だが遅い）
- **`empty?`**：配列が空かどうかを真偽値で返す
- **`any?`**：要素が1つでもあるかを真偽値で返す
- **`one?`**：要素が正確に1つかを真偽値で返す

### まとめ

`Array#size` は配列の要素数を取得する最も基本的なメソッドです。`length` と完全に同一であり、O(1) の高速な操作です。条件なしで全要素数を知りたい場合は `size` または `length` を使い、条件付きで数えたい場合は `count` を使用しましょう。
