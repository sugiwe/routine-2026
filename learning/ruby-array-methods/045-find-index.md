# Array#find_index

- 日付: 2026-02-14
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-find_index
- daily 記録: [daily/2026-02-14.md](../../daily/2026-02-14.md)

## 基本情報

### メソッドシグネチャ

```ruby
find_index(object) → integer or nil
find_index {|element| ... } → integer or nil
find_index → new_enumerator
```

### 概要

指定された要素の 0 始まりの整数インデックスを返します。見つからない場合は `nil` を返します。

### エイリアス

`index` メソッドとしても使用可能です。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# 引数 object のみが指定された場合、object と等しい最初の要素のインデックスを返す
a = [:foo, 'bar', 2, 'bar']
a.find_index('bar') # => 1

# 該当する要素が見つからない場合は、nil を返す

# ブロックのみが与えられた場合、ブロックが真値を返した最初の要素のインデックスを返す
a = [:foo, 'bar', 2, 'bar']
a.find_index {|element| element == 'bar' } # => 1

# ブロックが一度も真値を返さなかった場合、nil を返す

# 引数もブロックも与えられなかった場合、新しい Enumerator を返す
```

## 深掘り・補足

### 3 つの使用パターン

#### 1. オブジェクト検索（引数あり）

```ruby
array = ['apple', 'banana', 'cherry', 'banana']
array.find_index('banana')  # => 1 (最初に見つかった位置)
array.find_index('grape')   # => nil (見つからない)
```

**ポイント**: `==` による比較なので、同値性が重要です。

#### 2. ブロック検索（条件指定）

```ruby
numbers = [1, 3, 5, 8, 10, 12]
numbers.find_index { |n| n.even? }  # => 3 (最初の偶数は 8、インデックス 3)
numbers.find_index { |n| n > 100 }  # => nil (条件を満たす要素なし)
```

**ポイント**: より複雑な検索条件を指定できます。

#### 3. Enumerator の生成（引数・ブロックなし）

```ruby
array = [10, 20, 30]
enum = array.find_index
# => #<Enumerator: [10, 20, 30]:find_index>
```

### 重要な注意点

#### 最初の一致のみ

`find_index` は最初に条件を満たした要素のインデックスのみを返します。すべての一致を探す場合は別の方法が必要です:

```ruby
# すべての一致を見つける場合
array = ['a', 'b', 'a', 'c', 'a']
all_indices = array.each_index.select { |i| array[i] == 'a' }
# => [0, 2, 4]
```

#### nil との区別

```ruby
array = [nil, 'foo', 'bar']
array.find_index(nil)  # => 0 (nil は配列の最初にある)
array.find_index('baz') # => nil (見つからない)
```

インデックス 0 と「見つからない」場合の `nil` を区別する必要があります。

### 関連メソッドとの比較

| メソッド | 返り値 | 用途 |
|---------|--------|------|
| `find_index` | インデックス or nil | 要素の位置を知りたい |
| `find` | 要素 or nil | 要素自体を取得したい |
| `include?` | true or false | 存在確認のみ |

### パフォーマンスの考慮事項

- **時間計算量**: O(n) - 配列を先頭から順に探索
- **最悪ケース**: 配列の末尾に要素がある、または存在しない場合
- **大規模配列**: 頻繁に検索する場合は Hash や Set の使用を検討

### 実践的なユースケース

```ruby
# ユーザーIDから配列内の位置を特定
users = [{id: 1, name: 'Alice'}, {id: 2, name: 'Bob'}, {id: 3, name: 'Charlie'}]
position = users.find_index { |u| u[:id] == 2 }
# => 1

# 条件を満たす最初の要素の前後を取得
numbers = [1, 2, 3, 4, 5, 6]
index = numbers.find_index { |n| n > 3 }
before = numbers[0...index]  # => [1, 2, 3]
after = numbers[index..-1]   # => [4, 5, 6]
```

`find_index` は配列操作における基本的かつ強力なメソッドで、条件に基づいた要素の位置特定に不可欠です。
