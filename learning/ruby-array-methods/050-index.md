# Array#index

- 日付: 2026-02-19
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-index
- daily 記録: [daily/2026-02-19.md](../../daily/2026-02-19.md)

## 基本情報

`find_index` のエイリアスです。

```ruby
index(object) → integer or nil
index {|element| ... } → integer or nil
index → new_enumerator
```

指定された要素の 0 始まりのインデックスを返します。見つからない場合は `nil` を返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

`find_index` と同じ

### コード例

```ruby
# index(object) → integer or nil
# index {|element| ... } → integer or nil
# index → new_enumerator

# Returns the zero-based integer index of a specified element, or nil.
# 指定された要素の0始まりの整数インデックスを返します。見つからない場合はnilを返します。
# With only argument object given, returns the index of the first element element for which object == element:
# 引数objectのみが与えられた場合、object == elementとなる最初の要素elementのインデックスを返します。
a = [:foo, 'bar', 2, 'bar']
a.index('bar') # => 1

# Returns nil if no such element found.
# 該当する要素が見つからない場合、nilを返します。
# With only a block given, calls the block with each successive element; returns the index of the first element for which the block returns a truthy value:
# ブロックのみが与えられた場合、ブロックを各要素で順次呼び出す。ブロックが真値を返す最初の要素のインデックスを返します。
a = [:foo, 'bar', 2, 'bar']
a.index {|element| element == 'bar' } # => 1

# Returns nil if the block never returns a truthy value.
# ブロックが真値を一度も返さない場合はnilを返します。
# With neither an argument nor a block given, returns a new Enumerator.
# 引数もブロックも与えられなかった場合、新しいEnumeratorを返します。

# Related: see Methods for Querying.
# Alias for: find_index
```

## 深掘り・補足

### `find_index` との関係

`index` は `find_index` の**エイリアス**です。どちらを使っても動作は完全に同一です。

```ruby
a = [10, 20, 30, 20]
a.index(20)      # => 1
a.find_index(20) # => 1  # 同じ結果
```

歴史的に `index` の方が古くから存在し、`find_index` は `Enumerable` モジュールとの一貫性のために追加されました。

### 3つの使い方

**1. オブジェクトを直接指定（`==` で比較）**

```ruby
a = [:foo, 'bar', 2]
a.index(:foo)  # => 0
a.index('bar') # => 1
a.index(2)     # => 2
a.index(:baz)  # => nil（見つからない場合）
```

**2. ブロックで条件指定**

```ruby
a = [1, 2, 3, 4, 5]
a.index {|n| n > 3 }  # => 3（最初に条件を満たす要素のインデックス）
a.index {|n| n > 10 } # => nil
```

**3. 引数もブロックもなし → Enumerator を返す**

```ruby
a = [:foo, 'bar', 2]
enum = a.index
# => #<Enumerator: [:foo, "bar", 2]:index>
enum.each {|el| el == 'bar' } # => 1
```

### 重要な注意点

- **最初にマッチした要素のインデックスを返す**（重複がある場合は先頭側）
- 比較は `==` を使うため、型が違っても `==` が `true` になれば一致とみなされる

```ruby
a = [1, 2, 3]
a.index(2.0) # => 1（整数2と浮動小数点2.0は == で等しい）
```

### 関連メソッドとの比較

| メソッド | 説明 |
|---|---|
| `index` / `find_index` | 最初にマッチした要素のインデックスを返す |
| `rindex` | 最後にマッチした要素のインデックスを返す |
| `include?` | 含まれているかどうかの真偽値を返す |
| `find` / `detect` | インデックスではなく要素自体を返す |

```ruby
a = ['a', 'b', 'a']
a.index('a')  # => 0（最初）
a.rindex('a') # => 2（最後）
```

### パフォーマンスの考慮事項

配列を先頭から線形探索するため、計算量は O(n) です。頻繁に検索する場合は Hash の使用を検討してください。

```ruby
# 配列での検索: O(n)
arr = [1, 2, 3, ..., 1000]
arr.index(500) # 最悪500回の比較

# ハッシュでの検索: O(1)
hash = arr.each_with_index.to_h
hash[500] # => 499（即座に取得）
```
