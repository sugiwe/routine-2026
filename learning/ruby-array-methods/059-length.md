# Array#length

- 日付: 2026-02-28
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-length
- daily 記録: [daily/2026-02-28.md](../../daily/2026-02-28.md)

## 基本情報

```ruby
array.length → integer
```

配列に含まれる要素の個数を返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

size のエイリアス

### コード例

```ruby
# Returns the count of elements in self:
# 自身の要素数を返します。
[0, 1, 2].length # => 3
[].length        # => 0

# Related: see Methods for Querying.
# Also aliased as: size
```

## 深掘り・補足

### 重要なポイント

1. **エイリアスの関係**
   - `length` と `size` は完全に同じ機能を持つ
   - どちらを使っても動作は同一（好みや慣習で選択）
   - 一般的には `length` の方が若干多く使われる傾向がある

2. **戻り値は非負整数**
   - 空の配列の場合は `0` を返す
   - 要素がある場合は正の整数を返す

3. **計算量**
   - O(1) の定数時間で実行される
   - Ruby の配列は内部的にサイズ情報を保持しているため、要素を数える必要がない

### ユースケース

**配列のインデックス範囲を確認**
```ruby
arr = ['a', 'b', 'c']
last_index = arr.length - 1  # => 2
arr[last_index]  # => 'c'
```

**ループの制御**
```ruby
arr = [10, 20, 30]
(0...arr.length).each do |i|
  puts "Index #{i}: #{arr[i]}"
end
```

**配列が空かどうかの判定**
```ruby
arr = []
if arr.length == 0
  puts "配列は空です"
end

# ただし、空かどうかの判定には empty? の方が意図が明確
arr.empty?  # => true
```

### 関連メソッドとの比較

**`length` vs `size`**
- 完全に同じ機能（エイリアス）
- どちらを使っても OK

**`length` vs `count`**
```ruby
arr = [1, 2, 3, 4, 5]

# length: 単純に要素数を返す
arr.length  # => 5

# count: ブロックを渡すと、条件を満たす要素の数を返す
arr.count { |x| x > 3 }  # => 2

# count: 引数を渡すと、その値と等しい要素の数を返す
[1, 2, 3, 2, 1].count(2)  # => 2
```

**`length` vs `empty?`**
```ruby
arr = []

# length を使って空かどうかを判定
arr.length == 0  # => true

# empty? を使った方が意図が明確
arr.empty?  # => true
```

### 注意点

1. **nil を含む配列でも正しく数える**
   ```ruby
   [nil, nil, nil].length  # => 3
   ```

2. **文字列の length とは別物**
   ```ruby
   "hello".length  # => 5 (文字列の文字数)
   ["hello"].length  # => 1 (配列の要素数)
   ```

3. **多次元配列では第一階層のみ**
   ```ruby
   [[1, 2], [3, 4, 5]].length  # => 2（内側の配列の要素数ではない）
   ```

### パフォーマンスの考慮事項

`length` は O(1) で実行されるため、パフォーマンスを気にする必要はありません。配列のサイズ情報は内部的に保持されており、要素を数え直す必要がないためです。

ループ内で何度も呼び出しても問題ありません：
```ruby
# これは効率的
arr = [1, 2, 3, 4, 5]
(0...arr.length).each do |i|
  puts arr[i]
end
```

### まとめ

`Array#length` は配列の基本的なメソッドで、要素数を取得する最もシンプルな方法です。ユーザーが指摘した通り `size` のエイリアスであり、どちらを使っても同じ結果が得られます。配列操作の基礎として頻繁に使用されるメソッドです。
