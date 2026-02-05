# Array#each_index

- 日付: 2026-02-05
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-each_index
- daily 記録: [daily/2026-02-05.md](../../daily/2026-02-05.md)

## 基本情報

```ruby
each_index {|index| ... } → self
each_index → new_enumerator
```

ブロックが与えられた場合、自身の要素を繰り返し処理し、各配列のインデックスをブロックに渡します。そして、自身を返します。

ブロックが与えられない場合、新しい Enumerator を返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

ブロックが与えられた場合、自身の要素を繰り返し処理し、各配列のインデックスをブロックに渡す。そして、自身を返す

### コード例

```ruby
# each_index {|index| ... } → self
# each_index → new_enumerator
# With a block given, iterates over the elements of self, passing each array index to the block; returns self:
# ブロックが与えられた場合、自身の要素を繰り返し処理し、各配列のインデックスをブロックに渡します。そして、自身を返します。
a = [:foo, 'bar', 2]
a.each_index {|index|  puts "#{index} #{a[index]}" }

# Output:
0 foo
1 bar
2 2

# Allows the array to be modified during iteration:
# 配列を反復処理中に変更できます。
a = [:foo, 'bar', 2]
a.each_index {|index| puts index; a.clear if index > 0 }
a # => []

# Output:
0
1

# With no block given, returns a new Enumerator.
# ブロックが与えられない場合、新しいEnumeratorを返します。
```

## 深掘り・補足

### 重要なポイント

1. **要素ではなくインデックスを処理**
   - `each` が要素そのものを渡すのに対し、`each_index` はインデックス（0, 1, 2, ...）を渡します
   - 要素にアクセスする必要がある場合は `array[index]` で取得します

2. **self を返す**
   - メソッドチェーンが可能です
   - 反復処理後も元の配列を操作できます

3. **反復中の配列変更が可能**
   - コード例にあるように、反復処理中に配列を変更できます
   - ただし、予期しない動作を招く可能性があるため注意が必要です

### ユースケース

#### 1. インデックスベースの条件判定

```ruby
# 偶数インデックスの要素だけを処理
fruits = ['apple', 'banana', 'cherry', 'date']
fruits.each_index do |i|
  puts fruits[i] if i.even?
end
# Output:
# apple
# cherry
```

#### 2. 複数配列の同時処理

```ruby
names = ['Alice', 'Bob', 'Charlie']
ages = [25, 30, 35]

names.each_index do |i|
  puts "#{names[i]} is #{ages[i]} years old"
end
# Output:
# Alice is 25 years old
# Bob is 30 years old
# Charlie is 35 years old
```

#### 3. インデックスと要素の両方が必要な処理

```ruby
scores = [85, 92, 78, 95]
scores.each_index do |i|
  puts "Student #{i + 1}: #{scores[i]} points"
end
# Output:
# Student 1: 85 points
# Student 2: 92 points
# Student 3: 78 points
# Student 4: 95 points
```

### 注意点

1. **each_with_index の方が便利な場合が多い**
   - 要素とインデックスの両方が必要な場合は `each_with_index` の方が直感的です

   ```ruby
   # each_index を使う場合
   array.each_index { |i| puts "#{i}: #{array[i]}" }

   # each_with_index を使う方が明快
   array.each_with_index { |elem, i| puts "#{i}: #{elem}" }
   ```

2. **反復中の配列変更は慎重に**
   - `clear` や `delete_at` などで配列を変更すると、意図しない動作になる可能性があります
   - 特に、サイズが変わるとインデックスがずれるため注意が必要です

3. **空配列の場合**
   ```ruby
   [].each_index { |i| puts i }  # 何も出力されない
   ```

### 関連メソッドとの比較

| メソッド | 引数 | 用途 |
|---------|------|------|
| `each` | 要素 | 要素を順に処理 |
| `each_index` | インデックス | インデックスを順に処理 |
| `each_with_index` | 要素、インデックス | 両方必要な場合 |
| `map.with_index` | 要素、インデックス | 変換結果を新しい配列で返す |

### パフォーマンスの考慮事項

- `each_index` 自体は効率的ですが、ブロック内で `array[index]` を呼ぶと若干のオーバーヘッドがあります
- 要素だけが必要なら `each` を、要素とインデックスの両方が必要なら `each_with_index` を使う方が効率的です

### 実践的なアドバイス

- **純粋にインデックスだけが必要な場合に使う**: 例えば、インデックスを別の配列の添字として使う場合
- **要素も必要なら `each_with_index` を検討**: コードが読みやすくなります
- **Range と組み合わせる選択肢も**: `(0...array.size).each { |i| ... }` も同様の処理が可能
