# Array#uniq

- 日付: 2026-04-08
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-uniq
- daily 記録: [daily/2026-04-08.md](../../daily/2026-04-08.md)

## 基本情報

```ruby
uniq → new_array
uniq { |element| ... } → new_array
```

**Array#uniq** は、配列から重複要素を取り除いた新しい配列を返す**非破壊的メソッド**です。最初に出現した要素は常に保持されます。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# Returns a new array containing those elements from self that are not duplicates, the first occurrence always being retained.
# 自分自身の要素から重複を取り除いた新しい配列を返します。その際、最初に出現した要素は常に保持されます。

# With no block given, identifies and omits duplicate elements using method eql? to compare elements:
# ブロックが指定されていない場合、`eql?`メソッドを使用して要素を比較し、重複する要素を特定して除外します。
a = [0, 0, 1, 1, 2, 2]
a.uniq # => [0, 1, 2]

# With a block given, calls the block for each element; identifies and omits "duplicate" elements using method eql? to compare block return values; that is, an element is a duplicate if its block return value is the same as that of a previous element:
# ブロックが与えられた場合、各要素に対してそのブロックを呼び出します。ブロックの戻り値を比較するために `eql?` メソッドを使用して「重複」する要素を特定し、それらを取り除きます。つまり、ある要素のブロックの戻り値が、それ以前に出現した要素の戻り値と同じである場合、その要素は重複とみなされます。
a = ['a', 'aa', 'aaa', 'b', 'bb', 'bbb']
a.uniq {|element| element.size } # => ["a", "aa", "aaa"]

# Related: Methods for Fetching.
```

## 深掘り・補足

### 重要なポイント

#### 1. **非破壊的メソッド**

`uniq` は元の配列を変更せず、新しい配列を返します。元の配列を直接変更したい場合は、破壊的版の `uniq!` を使用します。

```ruby
arr = [2, 5, 6, 556, 6, 6, 8, 9, 0, 123, 556]
result = arr.uniq  # => [2, 5, 6, 556, 8, 9, 0, 123]
arr                # => [2, 5, 6, 556, 6, 6, 8, 9, 0, 123] (元の配列は変わらない)

arr.uniq!          # => [2, 5, 6, 556, 8, 9, 0, 123]
arr                # => [2, 5, 6, 556, 8, 9, 0, 123] (元の配列が変わる)
```

#### 2. **最初の出現を保持**

重複がある場合、**最初に出現した要素**が保持されます。順序が重要な場合、この動作は便利です。

```ruby
[3, 1, 2, 1, 3].uniq  # => [3, 1, 2] (最初の3と1が保持される)
```

#### 3. **ブロックでカスタム比較**

ブロックを渡すと、ブロックの戻り値を比較して重複を判定します。これにより、「特定の属性が同じ要素」を重複とみなすことができます。

```ruby
# 文字列の長さで重複判定
['a', 'aa', 'aaa', 'b', 'bb', 'bbb'].uniq { |s| s.size }
# => ["a", "aa", "aaa"]
# 'a' と 'b' は長さ1で重複 → 'a' を保持
# 'aa' と 'bb' は長さ2で重複 → 'aa' を保持
# 'aaa' と 'bbb' は長さ3で重複 → 'aaa' を保持
```

### ユースケース

#### 1. **重複データの除去**

```ruby
# ユーザーIDリストから重複を除去
user_ids = [1, 2, 3, 2, 1, 4, 3]
unique_ids = user_ids.uniq  # => [1, 2, 3, 4]
```

#### 2. **ユニークな値のリスト作成**

```ruby
# タグの重複を除去
tags = ['ruby', 'rails', 'ruby', 'javascript', 'rails']
unique_tags = tags.uniq  # => ['ruby', 'rails', 'javascript']
```

#### 3. **オブジェクトの属性で重複判定**

```ruby
# ユーザーのメールアドレスで重複を除去
users = [
  { id: 1, email: 'alice@example.com' },
  { id: 2, email: 'bob@example.com' },
  { id: 3, email: 'alice@example.com' }
]

unique_users = users.uniq { |u| u[:email] }
# => [
#   { id: 1, email: 'alice@example.com' },
#   { id: 2, email: 'bob@example.com' }
# ]
```

### 関連メソッドとの比較

| メソッド | 動作 | 破壊的 |
|---------|------|--------|
| `uniq` | 重複を除去した新しい配列を返す | ❌ |
| `uniq!` | 重複を除去して元の配列を変更 | ✅ |
| `compact` | nil要素を除去 | ❌ |
| `compact!` | nil要素を除去して元の配列を変更 | ✅ |
| `select` | 条件に合う要素だけを抽出 | ❌ |
| `reject` | 条件に合わない要素だけを抽出 | ❌ |

### パフォーマンスの考慮事項

#### 内部実装

`uniq` は内部でハッシュを使用して重複を検出します。そのため、時間計算量は **O(n)** です（要素数に比例）。

#### 注意点

- **`eql?` と `hash` を使用**: `uniq` は要素の比較に `eql?` メソッドを、ハッシュキーとして `hash` メソッドを使用します。カスタムオブジェクトで `uniq` を使う場合、これらのメソッドを適切にオーバーライドする必要があります。

```ruby
class User
  attr_reader :id, :name

  def initialize(id, name)
    @id = id
    @name = name
  end

  # uniq で正しく動作するために必要
  def eql?(other)
    self.class == other.class && id == other.id
  end

  def hash
    id.hash
  end
end

users = [
  User.new(1, 'Alice'),
  User.new(2, 'Bob'),
  User.new(1, 'Alice (duplicate)')
]

users.uniq  # => [User.new(1, 'Alice'), User.new(2, 'Bob')]
```

### `union` メソッドとの違い

前回学習した `union` メソッドは、複数の配列を統合して重複を除去します。一方、`uniq` は単一の配列内の重複を除去します。

```ruby
# uniq: 1つの配列内の重複を除去
[1, 2, 2, 3].uniq  # => [1, 2, 3]

# union: 複数の配列を統合して重複を除去
[1, 2].union([2, 3], [3, 4])  # => [1, 2, 3, 4]
```

---

**まとめ**: `Array#uniq` は、配列から重複要素を除去する非破壊的メソッドです。ブロックを使えばカスタム比較も可能で、データのクレンジングやユニークな値のリスト作成に便利です。内部でハッシュを使用するため、効率的に動作します。
