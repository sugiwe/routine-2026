# Array#select

- 日付: 2026-03-23
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-select
- daily 記録: [daily/2026-03-23.md](../../daily/2026-03-23.md)

## 基本情報

```ruby
select {|element| ... } → new_array
select → new_enumerator
```

配列の要素をフィルタリングし、**ブロック条件を満たす要素のみを含む新しい配列を返す**非破壊的なメソッドです。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

（メモなし）

### コード例

```ruby
# With a block given, calls the block with each element of self; returns a new array containing those elements of self for which the block returns a truthy value:
# ブロックが与えられた場合、selfの各要素に対してそのブロックを呼び出します。ブロックの評価結果が真（truthy）となる要素を集めた新しい配列を返します。
a = [:foo, 'bar', 2, :bam]
a.select {|element| element.to_s.start_with?('b') }
# => ["bar", :bam]

# With no block given, returns a new Enumerator.
# Related: see Methods for Fetching.
# Also aliased as: filter
```

## 深掘り・補足

### 重要なポイント

#### 1. 非破壊的メソッド

`select` は元の配列を変更せず、新しい配列を返します。

```ruby
arr = [1, 2, 3, 4, 5, 6]
result = arr.select {|a| a > 3}  # => [4, 5, 6]
arr                               # => [1, 2, 3, 4, 5, 6]（変わらない）
```

#### 2. filter との関係

`select` は `filter` というエイリアスを持ちます。実質的には同一のメソッドです。

```ruby
arr.select {|a| a > 3}  # => [4, 5, 6]
arr.filter {|a| a > 3}  # => [4, 5, 6]（同じ結果）
```

どちらを使っても動作は同じですが、Ruby コミュニティでは `select` がより一般的です。

#### 3. 戻り値

- **条件に合致した要素がある場合**: それらを含む新しい配列
- **条件に該当する要素がない場合**: 空配列 `[]`
- **ブロックが与えられない場合**: `Enumerator` オブジェクト

```ruby
[1, 2, 3].select {|n| n > 10}  # => []

# ブロックなしの場合、Enumeratorを返す
enumerator = [1, 2, 3].select
enumerator.select(&:even?)  # => [2]
```

### ユースケース

#### 1. 数値のフィルタリング

```ruby
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# 偶数だけを抽出
numbers.select(&:even?)  # => [2, 4, 6, 8, 10]

# 3より大きい数を抽出
numbers.select {|n| n > 3}  # => [4, 5, 6, 7, 8, 9, 10]

# 複合条件
numbers.select {|n| n.even? && n > 5}  # => [6, 8, 10]
```

#### 2. 文字列のフィルタリング

```ruby
words = %w[apple banana cherry date elderberry]

# 5文字より長い単語
words.select {|w| w.length > 5}  # => ["banana", "cherry", "elderberry"]

# 特定の文字で始まる単語
words.select {|w| w.start_with?('b')}  # => ["banana"]
```

#### 3. オブジェクトのフィルタリング

```ruby
users = [
  { name: 'Alice', age: 25 },
  { name: 'Bob', age: 30 },
  { name: 'Charlie', age: 35 }
]

# 30歳以上のユーザー
users.select {|u| u[:age] >= 30}
# => [{:name=>"Bob", :age=>30}, {:name=>"Charlie", :age=>35}]
```

#### 4. nil や false の除外

```ruby
values = [1, nil, 2, false, 3, nil, 4]

# 真値（truthy）だけを抽出
values.select {|v| v}  # => [1, 2, 3, 4]

# または compact を使う（nil のみ除外）
values.compact  # => [1, 2, false, 3, 4]
```

### 関連メソッドとの比較

| メソッド | 説明 | 破壊的 | 戻り値 |
|---------|------|--------|--------|
| `select` | 条件を満たす要素を返す | ❌ | 新しい配列 |
| `select!` | 条件を満たす要素を残す | ✅ | self または nil |
| `filter` | `select` のエイリアス | ❌ | 新しい配列 |
| `reject` | 条件を満たさない要素を返す | ❌ | 新しい配列 |
| `reject!` | 条件を満たさない要素を残す | ✅ | self または nil |
| `keep_if` | 条件を満たす要素を残す | ✅ | self |
| `delete_if` | 条件を満たす要素を削除 | ✅ | self |

#### select vs reject

```ruby
arr = [1, 2, 3, 4, 5, 6]

arr.select {|n| n.even?}  # => [2, 4, 6]（偶数を選択）
arr.reject {|n| n.even?}  # => [1, 3, 5]（偶数を拒否）
```

#### select vs select!

```ruby
arr = [1, 2, 3, 4, 5, 6]

# 非破壊的
arr.select {|n| n.even?}  # => [2, 4, 6]
arr                        # => [1, 2, 3, 4, 5, 6]（変わらない）

# 破壊的
arr.select! {|n| n.even?}  # => [2, 4, 6]
arr                         # => [2, 4, 6]（変更される）
```

### パフォーマンスの考慮事項

#### 1. ブロックの複雑さ

ブロック内の処理が複雑な場合、全要素に対して実行されるため注意が必要です。

```ruby
# 悪い例：毎回データベースにアクセス
users.select {|u| User.find(u.id).active? }

# 良い例：事前に必要なデータを取得
active_user_ids = User.where(active: true).pluck(:id)
users.select {|u| active_user_ids.include?(u.id) }
```

#### 2. 大きな配列のフィルタリング

非常に大きな配列の場合、遅延評価を考慮することも選択肢です。

```ruby
# 即座に配列を生成
(1..1_000_000).to_a.select {|n| n.even?}

# 遅延評価（必要な分だけ処理）
(1..1_000_000).lazy.select {|n| n.even?}.first(10)
```

### 注意点

#### 1. ブロックがない場合

ブロックを渡さない場合、`Enumerator` が返されます。

```ruby
enumerator = [1, 2, 3].select
enumerator.each {|n| n.even?}  # => [2]
```

#### 2. 破壊的メソッドの戻り値

`select!` は、変更がなかった場合に `nil` を返します。

```ruby
arr = [2, 4, 6]
arr.select! {|n| n.even?}  # => nil（すべて偶数なので変更なし）
```

#### 3. 真偽値の判定

Ruby では `nil` と `false` 以外はすべて真（truthy）として扱われます。

```ruby
[0, '', [], {}, nil, false].select {|v| v}
# => [0, "", [], {}]（0や空文字列なども truthy）
```

### まとめ

`Array#select` は、配列から特定の条件を満たす要素だけを抽出する強力なメソッドです。

- **非破壊的**: 元の配列は変更されない
- **直感的**: ブロック条件が真となる要素を選択
- **汎用性が高い**: さまざまなフィルタリング条件に対応
- **エイリアス**: `filter` としても使える
- **関連メソッド**: `reject`（逆の条件）、`select!`（破壊的版）など

配列操作の基本メソッドとして、頻繁に使用される重要なメソッドです。
