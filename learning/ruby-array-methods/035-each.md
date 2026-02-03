# Array#each

- 日付: 2026-02-04
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-each
- daily 記録: [daily/2026-02-04.md](../../daily/2026-02-04.md)

## 基本情報

```ruby
each {|element| ... } → self
each → new_enumerator
```

配列の各要素を順番にブロックに渡して処理を実行し、配列自身を返します。ブロックが与えられない場合は、新しい Enumerator を返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

ブロックが与えられた場合、自身の要素を繰り返し処理し、各要素をブロックに渡し、自身を返す

### コード例

```ruby
# each {|element| ... } → self
# each → new_enumerator

# With a block given, iterates over the elements of self, passing each element to the block; returns self:
# ブロックが与えられた場合、自身の要素を繰り返し処理し、各要素をブロックに渡します。そして自身を返します。
a = [:foo, 'bar', 2]
a.each {|element|  puts "#{element.class} #{element}" }
# Output:
Symbol foo
String bar
Integer 2

# Allows the array to be modified during iteration:
# 配列のイテレーション中に変更を許可
a = [:foo, 'bar', 2]
a.each {|element| puts element; a.clear if element.to_s.start_with?('b') }
# Output:
foo
bar

# With no block given, returns a new Enumerator.
# ブロックが与えられない場合、新しいEnumerator（列挙子）を返します。
```

## 深掘り・補足

### 重要なポイント

1. **非破壊的操作**
   - 配列の内容自体は変更されない
   - 元の配列が返される（戻り値は `self`）

2. **Enumerable モジュールの中核**
   - Array は Enumerable をインクルードしている
   - `each` は Enumerable の基盤となるメソッド
   - 多くの便利なメソッド（`map`, `select`, `reduce` など）は内部で `each` を使用

3. **イテレーション中の変更**
   - ユーザーのコード例にあるように、イテレーション中に配列を変更することも可能
   - ただし、予期しない動作になる可能性があるため注意が必要

### ユースケース

```ruby
# 基本的な繰り返し処理
users.each do |user|
  puts "Hello, #{user.name}!"
end

# 副作用のある処理（メール送信など）
emails.each do |email|
  send_notification(email)
end

# 戻り値を利用したメソッドチェーン
arr = [1, 2, 3]
  .each { |n| puts n }
  .map { |n| n * 2 }
# 配列自身が返されるので、続けて他のメソッドを呼べる
```

### 注意点

1. **戻り値は元の配列**
   - `map` のように変換した新しい配列は返さない
   - 変換が必要な場合は `map` を使用

2. **イテレーション中の配列変更は危険**
   ```ruby
   # 危険な例
   arr = [1, 2, 3, 4, 5]
   arr.each do |n|
     arr.delete(n) if n.even?  # 予期しない動作になる可能性
   end
   ```

3. **ブロックなしの呼び出し**
   - Enumerator が返される
   - 遅延評価や外部イテレータとして利用可能

### 関連メソッドとの比較

| メソッド | 戻り値 | 用途 |
|---------|--------|------|
| `each` | 元の配列 | 副作用のある処理 |
| `map` | 新しい配列 | 要素の変換 |
| `select` | 新しい配列 | 条件に合う要素の抽出 |
| `reverse_each` | 元の配列 | 逆順での繰り返し |

### パフォーマンスの考慮事項

- `each` は Ruby で最も基本的かつ高速な繰り返し処理
- C 言語レベルで最適化されている
- 単純な繰り返しには `each` が最適
