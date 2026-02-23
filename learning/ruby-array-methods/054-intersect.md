# Array#intersect?

- 日付: 2026-02-23
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-intersect-3F
- daily 記録: [daily/2026-02-23.md](../../daily/2026-02-23.md)

## 基本情報

```ruby
intersect?(other_array) → true or false
```

2つの配列が**少なくとも1つの共通要素を持つかどうか**を判定するメソッドです。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

これも初めて知った。

### コード例

```ruby
# Returns whether other_array has at least one element that is eql? to some element of self:
# `other_array` の要素の中に、`self` のいずれかの要素と `eql?` であるものが少なくとも1つ存在するかどうかを返します。
[1, 2, 3].intersect?([3, 4, 5]) # => true
[1, 2, 3].intersect?([4, 5, 6]) # => false

# Each element must correctly implement method hash.
# 各要素は、正しくハッシュメソッドを実装する必要があります。
# Related: see Methods for Querying.
```

## 深掘り・補足

### 重要なポイント

1. **戻り値はブール値のみ**
   - `true`: 共通要素が1つ以上存在する
   - `false`: 共通要素が1つもない

2. **比較には `eql?` を使用**
   - オブジェクトの等価性判定に `eql?` メソッドを使用
   - カスタムクラスでは `eql?` と `hash` の適切な実装が必要

3. **パフォーマンス特性**
   - 共通要素を実際に取得しない（判定のみ）
   - `intersection` や `&` よりも軽量で高速

### 実践的なユースケース

#### 1. 権限チェック

```ruby
user_permissions = [:read, :write, :delete]
required_permissions = [:admin, :delete]

# ユーザーが必要な権限のいずれかを持っているか
if user_permissions.intersect?(required_permissions)
  puts "アクセス許可"
else
  puts "アクセス拒否"
end
# => "アクセス許可" (delete が共通)
```

#### 2. タグの重複チェック

```ruby
article_tags = ["ruby", "rails", "backend"]
user_interests = ["frontend", "javascript", "react"]

if article_tags.intersect?(user_interests)
  puts "おすすめ記事です"
else
  puts "興味のない記事かもしれません"
end
# => "興味のない記事かもしれません"
```

#### 3. スケジュールの衝突判定

```ruby
meeting1_attendees = ["Alice", "Bob", "Charlie"]
meeting2_attendees = ["David", "Eve", "Frank"]

if meeting1_attendees.intersect?(meeting2_attendees)
  puts "参加者が重複しています"
else
  puts "同時開催可能です"
end
# => "同時開催可能です"
```

### 関連メソッドとの比較

| メソッド | 戻り値 | 用途 |
|---------|--------|------|
| `intersect?` | `true/false` | 共通要素の有無だけを知りたい場合 |
| `intersection` | 新しい配列 | 共通要素自体が必要な場合 |
| `&` | 新しい配列 | 共通要素を取得（重複除去あり） |
| `include?` | `true/false` | 単一要素の包含判定 |

#### コード例での比較

```ruby
a = [1, 2, 3]
b = [3, 4, 5]

a.intersect?(b)    # => true (判定のみ、高速)
a.intersection(b)  # => [3] (配列を返す)
a & b              # => [3] (intersection と同じ)
a.include?(3)      # => true (単一要素のみ)
```

### 注意点

1. **カスタムオブジェクトの場合**

   ```ruby
   class Person
     attr_reader :name

     def initialize(name)
       @name = name
     end

     # eql? と hash を適切に実装する必要がある
     def eql?(other)
       other.is_a?(Person) && name == other.name
     end

     def hash
       name.hash
     end
   end

   team1 = [Person.new("Alice"), Person.new("Bob")]
   team2 = [Person.new("Bob"), Person.new("Charlie")]

   team1.intersect?(team2)  # => true
   ```

2. **パフォーマンスの考慮**
   - 共通要素の有無だけを知りたい場合は `intersect?` を使う
   - 共通要素自体が必要な場合のみ `intersection` や `&` を使う

### いつ使うべきか

- ✅ 共通要素があるかどうかだけを知りたい
- ✅ パフォーマンスを重視したい
- ✅ 条件分岐で使用する
- ❌ 共通要素の内容が必要 → `intersection` を使う
- ❌ 単一要素の包含チェック → `include?` を使う
