# Array#none?

- 日付: 2026-03-05
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-none-3F
- daily 記録: [daily/2026-03-05.md](../../daily/2026-03-05.md)

## 基本情報

`Array#none?` は、自身の要素が指定された条件を **1つも満たさない** 場合に `true` を返し、それ以外の場合は `false` を返すメソッドです。

```ruby
none? → true or false
none?(object) → true or false
none? { |element| ... } → true or false
```

## ユーザーの学習記録（daily より）

### 第一印象のメモ

否定を判定するのって頭が混乱するな、、、

### コード例

```ruby
# Returns true if no element of self meets a given criterion, false otherwise.
# 自身の要素が指定された条件を1つも満たさない場合にtrueを返し、それ以外の場合はfalseを返します。
# With no block given and no argument, returns true if self has no truthy elements, false otherwise:
# ブロックも引数も指定しない場合、自身に真となる要素が一つもなければ`true`を、そうでなければ`false`を返します。
[nil, false].none?    # => true
[nil, 0, false].none? # => false
[].none?              # => true

# With argument object given, returns false if for any element element, object === element; true otherwise:
# 引数として渡されたオブジェクトが要素のいずれかと厳密に一致する場合に`false`を返し、それ以外の場合は`true`を返します。
['food', 'drink'].none?(/bar/) # => true
['food', 'drink'].none?(/foo/) # => false 正規表現でfooを含む文字列があるのでfalse
['food', 'drink'].none?('foo') # => true 文字列でfooに一致するものはないのでtrue
[].none?(/foo/)                # => true
[0, 1, 2].none?(3)             # => true
[0, 1, 2].none?(1)             # => false

# With a block given, calls the block with each element in self; returns true if the block returns no truthy value, false otherwise:
# ブロックが与えられた場合、自身の各要素を引数としてブロックを呼び出します。ブロックが真の値を返さなかった場合は `true` を、それ以外の場合は `false` を返します。
[0, 1, 2].none? {|element| element > 3 } # => true
[0, 1, 2].none? {|element| element > 1 } # => false
```

## 深掘り・補足

### 重要なポイント

#### 1. 「none = 否定」の理解

ユーザーさんのメモにもある通り、`none?` は **否定の判定** なので、頭が混乱しやすいです。

- **`none?` が `true`** = 条件を満たす要素が **1つもない**
- **`none?` が `false`** = 条件を満たす要素が **1つでもある**

つまり、`none?` は「全部ダメ」を確認するメソッドです。

#### 2. 3つの使い方

| 使い方 | 説明 | 例 |
|--------|------|-----|
| 引数・ブロックなし | **真の要素**が1つもないか | `[nil, false].none? # => true` |
| 引数あり | 引数と `===` で一致する要素が1つもないか | `[1, 2].none?(3) # => true` |
| ブロックあり | ブロックが真を返す要素が1つもないか | `[1, 2].none? { \|n\| n > 3 } # => true` |

#### 3. 真偽値の扱い（引数・ブロックなし）

```ruby
[nil, false].none?    # => true  (nil と false は偽)
[nil, 0, false].none? # => false (0 は真なので false)
[].none?              # => true  (空配列は「真の要素なし」)
```

Ruby では `nil` と `false` **以外はすべて真** なので、`0` や空文字列 `""` も真として扱われます。

#### 4. 正規表現と文字列の違い

```ruby
['food', 'drink'].none?(/foo/) # => false (正規表現で部分一致)
['food', 'drink'].none?('foo') # => true  (文字列で完全一致)
```

- **正規表現** (`/foo/`): 「foo を含む」でマッチ → `'food'` がマッチするので `false`
- **文字列** (`'foo'`): 完全一致で判定 → `'foo'` という要素はないので `true`

### ユースケース

#### 1. バリデーション（すべてが条件を満たさないことを確認）

```ruby
# 無効な値が1つもないことを確認
errors = [nil, nil, nil]
errors.none? # => true (すべてエラーなし)

errors = [nil, "Error!", nil]
errors.none? # => false (エラーが1つでもある)
```

#### 2. フィルタリングの前チェック

```ruby
numbers = [1, 2, 3, 4, 5]

# 10以上の数値が1つもないか
numbers.none? { |n| n >= 10 } # => true
```

#### 3. 条件に該当するものがないことを確認

```ruby
users = [
  { name: 'Alice', admin: false },
  { name: 'Bob', admin: false }
]

# 管理者が1人もいないか
users.none? { |user| user[:admin] } # => true
```

### 関連メソッドとの比較

| メソッド | 意味 | 真を返す条件 |
|---------|------|-------------|
| `all?` | すべて | すべての要素が条件を満たす |
| `any?` | いずれか | 少なくとも1つの要素が条件を満たす |
| `one?` | 1つだけ | 正確に1つの要素だけが条件を満たす |
| `none?` | 1つもない | **すべての要素が条件を満たさない** |

### 注意点

#### 空配列の扱い

```ruby
[].none?              # => true
[].none? { |n| n > 0 } # => true
```

空配列は「条件を満たす要素が1つもない」ので常に `true` です。

#### 二重否定に注意

```ruby
# 「NG ではない要素が1つもない」= 「すべて NG」
items.none? { |item| !item.ng? }

# 分かりにくい！以下のほうが読みやすい
items.all? { |item| item.ng? }
```

否定を使う場合、二重否定にならないよう注意しましょう。`all?` などで表現できないか検討すると可読性が上がります。

### まとめ

`none?` は **「条件を満たす要素が1つもない」** ことを確認するメソッドです。否定の判定なので頭が混乱しやすいですが、「全部ダメ」を確認したいときに便利です。二重否定を避け、シンプルな条件式を心がけましょう。
