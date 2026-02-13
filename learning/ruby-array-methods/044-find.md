# Array#find

- 日付: 2026-02-13
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-find
- daily 記録: [daily/2026-02-13.md](../../daily/2026-02-13.md)

## 基本情報

```ruby
find(if_none_proc = nil) {|element| ... } → object or nil
find(if_none_proc = nil) → enumerator
```

ブロックが真値を返した最初の要素を返します。該当する要素が見つからない場合は `nil` を返すか、`if_none_proc` が指定されていればそのProcを呼び出してその戻り値を返します。

**別名:** `detect`

## 深掘り・補足

### if_none_proc パラメータの使い方

`if_none_proc` は、検索条件に一致する要素が見つからなかったときに実行されるProcオブジェクトです。これにより、デフォルト値を返したり、特定の処理を実行したりできます。

**基本的な使い方:**

```ruby
# Procオブジェクトを事前に定義
default_proc = proc { "見つかりませんでした" }
[1, 2, 3].find(default_proc) {|n| n > 10}  # => "見つかりませんでした"

# ラムダ式を使う場合
[1, 2, 3].find(-> { 0 }) {|n| n > 10}  # => 0

# より複雑な処理も可能
error_handler = proc { raise "該当する要素が見つかりません" }
# [1, 2, 3].find(error_handler) {|n| n > 10}  # => RuntimeError
```

### detect との関係

`detect` は `find` の完全なエイリアスです。機能的には全く同じで、好みや慣習によって使い分けられます：

```ruby
[1, 3, 5].find {|n| n > 2}    # => 3
[1, 3, 5].detect {|n| n > 2}  # => 3
```

一般的に、Ruby コミュニティでは `find` の方がよく使われる傾向があります。

### 重要なポイント

**1. 最初の一致だけを返す**

`find` は条件に一致する**最初の要素のみ**を返します。すべての一致する要素が必要な場合は `select` または `filter` を使います：

```ruby
[1, 3, 5, 7, 9].find {|n| n > 2}    # => 3（最初の一致）
[1, 3, 5, 7, 9].select {|n| n > 2}  # => [3, 5, 7, 9]（すべての一致）
```

**2. パフォーマンスの利点**

`find` は最初の一致を見つけた時点で探索を終了するため、大きな配列では `select` よりも効率的です：

```ruby
# 100万要素の配列で最初の偶数を探す場合
large_array = (1..1_000_000).to_a
large_array.find {|n| n.even?}   # 高速（2つ目で終了）
large_array.select {|n| n.even?}.first  # 低速（全要素を走査）
```

**3. ブロックなしの場合**

ブロックを渡さない場合は Enumerator を返すため、他のメソッドとチェーンできます：

```ruby
[1, 3, 5].find.with_index {|element, index| element > 2 && index > 0}  # => 3
```

### ユースケース

**1. デフォルト値を持つ検索**

```ruby
users = [{name: "Alice", age: 25}, {name: "Bob", age: 30}]
admin = users.find(-> { {name: "Guest", age: 0} }) {|u| u[:name] == "Admin"}
# => {name: "Guest", age: 0}
```

**2. 条件に一致する最初の要素を取得**

```ruby
products = [
  {name: "Apple", price: 100, in_stock: false},
  {name: "Banana", price: 50, in_stock: true},
  {name: "Orange", price: 80, in_stock: true}
]

first_available = products.find {|p| p[:in_stock]}
# => {name: "Banana", price: 50, in_stock: true}
```

**3. 複雑な条件での検索**

```ruby
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
first_divisible_by_3_and_even = numbers.find {|n| n % 3 == 0 && n.even?}
# => 6
```

### 関連メソッドとの比較

| メソッド            | 戻り値                           | 用途                 |
| ------------------- | -------------------------------- | -------------------- |
| `find` / `detect`   | 最初の一致する要素               | 単一の要素を見つける |
| `select` / `filter` | 一致する全要素の配列             | 複数の要素を抽出     |
| `find_all`          | `select` のエイリアス            | 複数の要素を抽出     |
| `find_index`        | 最初の一致する要素のインデックス | 位置を知りたい場合   |
| `any?`              | 真偽値                           | 存在確認のみ         |

### 注意点

1. **nil との混同**
   - 要素が `nil` の場合と見つからない場合を区別できない
   - この場合は `find_index` と組み合わせるか、`if_none_proc` を使う

```ruby
[nil, 1, 2].find {|n| n.nil?}  # => nil（見つかった）
[1, 2, 3].find {|n| n.nil?}    # => nil（見つからない）

# 区別する方法
[1, 2, 3].find_index {|n| n.nil?}.nil?  # => true（見つからない）
```

2. **副作用に注意**
   - ブロック内で配列を変更すると予期しない動作になる可能性がある

3. **Procオブジェクトの作成コスト**
   - `if_none_proc` に毎回新しいProcを作成すると非効率
   - 可能であれば再利用する
