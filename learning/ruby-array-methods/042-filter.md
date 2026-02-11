# Array#filter

- 日付: 2026-02-11
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-filter
- daily 記録: [daily/2026-02-11.md](../../daily/2026-02-11.md)

## 基本情報

```ruby
filter {|element| ... } → new_array
filter → new_enumerator
```

`Array#filter` は、配列の要素をブロック条件に基づいてフィルタリングし、条件に合致した要素のみを含む新しい配列を返すメソッドです。

**エイリアス関係:**

- `Array#filter` は `Array#select` のエイリアス
- `Array#select` は `Array#find_all` のエイリアスでもある
- つまり、`filter`、`select`、`find_all` はすべて同じ動作をする

## ユーザーの学習記録（daily より）

### 第一印象のメモ

select のエイリアスか。あと find_all のエイリアスでもあるか。detect のエイリアスは find だったかな？

### コード例

```ruby
# filter {|element| ... } → new_array
# filter → new_enumerator

# With a block given, calls the block with each element of self; returns a new array containing those elements of self for which the block returns a truthy value:
# ブロックが与えられた場合、自身の各要素に対してブロックを呼び出し、ブロックが真と評価された要素のみを含む新しい配列を返します。
a = [:foo, 'bar', 2, :bam]
a.filter {|element| element.to_s.start_with?('b') }
# => ["bar", :bam]

# With no block given, returns a new Enumerator.
# ブロックが与えられなかった場合は、新しい `Enumerator` を返します。
```

## 深掘り・補足

### メソッドの動作

1. **ブロック付き呼び出し**
   - 配列の各要素に対してブロックを実行
   - ブロックが真と評価される（truthy な値を返す）要素のみを選択
   - 選択された要素を含む**新しい配列**を返す（元の配列は変更されない）

2. **ブロックなし呼び出し**
   - `Enumerator` オブジェクトを返す
   - 後からブロックを渡したり、他の Enumerator メソッドと連鎖できる

### エイリアスの使い分け

```ruby
# 以下はすべて同じ動作
array.filter {|x| x > 5 }
array.select {|x| x > 5 }
array.find_all {|x| x > 5 }
```

**使い分けの慣習:**

- `filter`: 関数型プログラミングに慣れた開発者や、JavaScript などの他言語からの移行者に馴染みやすい
- `select`: Ruby で最も一般的に使われる表現
- `find_all`: やや古い表現だが、「すべて見つける」という意味が明確

### 関連メソッドとの違い

#### `find` (エイリアス: `detect`) との違い

```ruby
# filter/select: 条件に合うすべての要素を配列で返す
[1, 2, 3, 4, 5].filter {|x| x > 3 }
# => [4, 5]

# find/detect: 条件に合う最初の1つだけを返す（配列ではなく要素）
[1, 2, 3, 4, 5].find {|x| x > 3 }
# => 4
```

#### `reject` との関係

```ruby
# filter: 条件が真の要素を選択
[1, 2, 3, 4, 5].filter {|x| x.even? }
# => [2, 4]

# reject: 条件が真の要素を除外（filter の逆）
[1, 2, 3, 4, 5].reject {|x| x.even? }
# => [1, 3, 5]
```

### 実践的なユースケース

```ruby
# ユーザーリストから管理者のみを抽出
users.filter {|user| user.admin? }

# 有効な（nil や空でない）値のみを取得
values.filter {|v| v && !v.empty? }

# 数値の配列から正の数のみを選択
numbers.filter {|n| n.positive? }

# Enumerator を使った遅延評価
large_array.filter.with_index {|element, index| index.even? }
```

### パフォーマンスの考慮事項

- **新しい配列を生成**: 元の配列は変更されず、条件に合う要素のコピーを含む新しい配列を返す
- **破壊的版は存在しない**: `filter!` や `select!` は存在するが、挙動が異なる（後述）
- **大きな配列では注意**: メモリを消費するため、非常に大きな配列では `lazy` との組み合わせを検討

```ruby
# 大規模データの遅延評価
large_array.lazy.filter {|x| x > 100 }.first(10)
```

### 破壊的版 `filter!` / `select!` について

```ruby
# filter! / select! は元の配列を変更する
a = [1, 2, 3, 4, 5]
a.filter! {|x| x.even? }
# a は [2, 4] に変更される

# 通常の filter は元の配列を保持
a = [1, 2, 3, 4, 5]
b = a.filter {|x| x.even? }
# a は [1, 2, 3, 4, 5] のまま、b は [2, 4]
```

### 注意点

1. **ブロックの戻り値が重要**: ブロックは真偽値（truthy/falsy）を返す必要がある
   - Ruby では `nil` と `false` のみが falsy、それ以外はすべて truthy

2. **副作用に注意**: ブロック内で配列を変更するのは避けるべき

```ruby
# 悪い例（予測不能な動作）
a.filter {|x| a.delete(x) if x.even?; x.odd? }

# 良い例（純粋な条件判定のみ）
a.filter {|x| x.odd? }
```

### まとめ

`Array#filter` は、配列から条件に合う要素を抽出する基本的かつ強力なメソッドです。`select` や `find_all` と完全に同義であり、好みやチームの慣習に応じて使い分けることができます。関数型プログラミングのスタイルに慣れている場合は `filter` が自然に感じられるでしょう。
