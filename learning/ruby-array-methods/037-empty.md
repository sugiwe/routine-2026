# Array#empty?

- 日付: 2026-02-06
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-empty-3F
- daily 記録: [daily/2026-02-06.md](../../daily/2026-02-06.md)

## 基本情報

**メソッドシグネチャ:**
```ruby
empty? → true or false
```

**概要:**
配列の要素数がゼロの場合に `true` を返し、それ以外の場合は `false` を返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

`empty? → true or false`

`exists?` の反対かなと思ったが、そもそも `exists?` は Rails のメソッドで、Ruby のメソッドではなかった。

### コード例

```ruby
# Returns true if the count of elements in self is zero, false otherwise.
# 自身の要素数がゼロの場合にtrueを返し、それ以外の場合はfalseを返します。

# Methods for Querying
- all? # Returns whether all elements meet a given criterion.
- any? # Returns whether any element meets a given criterion.
- count # Returns the count of elements that meet a given criterion.
- empty? # Returns whether there are no elements.
- find_index (aliased as index) # Returns the index of the first element that meets a given criterion.
- hash # Returns the integer hash code.
- include? # Returns whether any element == a given object.
- length (aliased as size) # Returns the count of elements.
- none? # Returns whether no element == a given object.
- one? # Returns whether exactly one element == a given object.
- rindex # Returns the index of the last element that meets a given criterion.
```

## 深掘り・補足

### 重要なポイント

#### 1. シンプルで明確な判定メソッド

`empty?` は配列に要素が存在するかどうかを判定する、最もシンプルなメソッドの一つです。メソッド名が意図を明確に表現しているため、コードの可読性を高めます。

#### 2. 返り値は常に Boolean

- 要素が 0 個: `true`
- 要素が 1 個以上: `false`

```ruby
[].empty?        #=> true
[1].empty?       #=> false
[1, 2, 3].empty? #=> false
```

### ユースケース

#### ケース 1: 条件分岐での使用

```ruby
users = fetch_users_from_database

if users.empty?
  puts "ユーザーが見つかりませんでした"
else
  puts "#{users.length} 人のユーザーが見つかりました"
end
```

#### ケース 2: デフォルト値の設定

```ruby
results = search_results || []
message = results.empty? ? "結果なし" : "#{results.size} 件の結果"
```

#### ケース 3: エラーハンドリング

```ruby
def process_items(items)
  raise ArgumentError, "items cannot be empty" if items.empty?

  items.each { |item| process(item) }
end
```

### 関連メソッドとの比較

| メソッド | 用途 | 返り値の型 | 例 |
|---------|------|-----------|-----|
| `empty?` | 要素の有無を判定 | Boolean | `[].empty? #=> true` |
| `length` / `size` | 要素数を取得 | Integer | `[1, 2].length #=> 2` |
| `count` | 条件を満たす要素数 | Integer | `[1, 2, 3].count { \|n\| n > 1 } #=> 2` |
| `any?` | 要素が存在するか | Boolean | `[1].any? #=> true` |
| `none?` | 要素が存在しないか | Boolean | `[].none? #=> true` |

#### `empty?` vs `length == 0`

```ruby
# どちらも同じ結果だが、empty? の方が意図が明確
array.empty?      # 推奨: 可読性が高い
array.length == 0 # 同じ結果だが冗長
array.size == 0   # 同じ結果だが冗長
```

#### `empty?` vs `any?`

```ruby
# empty? と any? は逆の関係
[].empty?  #=> true
[].any?    #=> false

[1].empty? #=> false
[1].any?   #=> true

# 否定形を使う場合
!array.empty?  # 要素がある = array.any? と同じ
!array.any?    # 要素がない = array.empty? と同じ
```

### パフォーマンスの考慮事項

`empty?` は内部的に配列のサイズを **O(1)** 時間（定数時間）で確認します。これは、配列のサイズが内部的に保持されているためです。

```ruby
# どちらもO(1)で同等のパフォーマンス
array.empty?
array.length == 0

# しかし、empty? の方がコードの意図が明確
```

### 注意点

#### 1. `nil` に対しては使えない

```ruby
array = nil
array.empty? # NoMethodError: undefined method `empty?' for nil:NilClass
```

安全に使用するには：

```ruby
# パターン 1: 事前に nil チェック
array&.empty?  # nil の場合は nil を返す

# パターン 2: デフォルト値を設定
(array || []).empty?  # nil の場合は空配列として扱う

# パターン 3: nil チェックを明示
array.nil? || array.empty?
```

#### 2. `exists?` は Ruby の標準メソッドではない

ユーザーが第一印象で気づいた通り、`exists?` は Ruby 標準の Array メソッドではなく、Rails の ActiveRecord などで提供されるメソッドです。

```ruby
# Ruby 標準の配列
[].empty?   # OK: Ruby 標準メソッド
[].exists?  # NoMethodError

# Rails の ActiveRecord
User.exists?(id: 1)  # OK: Rails のメソッド
```

### Methods for Querying（クエリ系メソッド）との関係

ユーザーが記録したように、`empty?` は「Methods for Querying」カテゴリに分類されます。このカテゴリには、配列の状態を問い合わせる（判定する）メソッドが含まれています：

- `all?` - すべての要素が条件を満たすか
- `any?` - いずれかの要素が条件を満たすか
- `empty?` - 要素が存在しないか
- `none?` - どの要素も条件を満たさないか
- `one?` - ちょうど1つの要素が条件を満たすか

これらを組み合わせることで、配列の状態を柔軟に判定できます。

### まとめ

`empty?` は、配列が空かどうかを判定する最もシンプルで読みやすいメソッドです。`length == 0` と同じ結果を得られますが、コードの意図を明確に表現できるため、実務では `empty?` の使用が推奨されます。ただし、`nil` に対しては使えないため、安全に使用するには `&.empty?` や事前の nil チェックが必要です。
