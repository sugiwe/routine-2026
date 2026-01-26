# Array#cycle

- 日付: 2026-01-26
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-cycle

## 基本情報

```ruby
cycle(count = nil) {|element| ... } → nil
cycle(count = nil) → new_enumerator
```

配列の要素を繰り返し処理するメソッドです。`count` 引数で繰り返し回数を指定でき、省略すると無限に繰り返します。

## daily からの記録

### メモ

（記録なし）

### コード例・補足

```ruby
# With a block given, may call the block, depending on the value of argument count; count must be an integer-convertible object, or nil.
# ブロックが与えられた場合、引数の数に応じてブロックを呼び出すことができます。count は整数に変換可能なオブジェクト、または nil である必要があります。

# When count is positive, calls the block with each element, then does so repeatedly, until it has done so count times; returns nil:
# countが正の場合、各要素に対してブロックを呼び出し、count回繰り返します。nilを返します。
output = []
[0, 1].cycle(2) {|element| output.push(element) } # => nil
output # => [0, 1, 0, 1]

# When count is zero or negative, does not call the block:
# カウントがゼロまたはマイナスの場合は、ブロックを呼び出しません。
[0, 1].cycle(0) {|element| fail 'Cannot happen' }  # => nil
[0, 1].cycle(-1) {|element| fail 'Cannot happen' } # => nil

# When count is nil, cycles forever:
# count が nil の場合、無限ループします:

# Prints 0 and 1 forever.
# 0と1を無限に表示します。
[0, 1].cycle {|element| puts element }
[0, 1].cycle(nil) {|element| puts element }

# With no block given, returns a new Enumerator.
# ブロックが指定されていない場合、新しいEnumeratorを返します。
```

## 深掘り・補足

### 重要なポイント

#### 1. 戻り値の特性

- **ブロック付きで呼び出した場合**: 常に `nil` を返す
- **ブロックなしの場合**: `Enumerator` オブジェクトを返す

これは他の多くの繰り返しメソッド（`each`, `map` など）とは異なる動作です。

#### 2. count パラメータの振る舞い

```ruby
# count の値による動作の違い
arr = ['a', 'b', 'c']

# 正の整数: 配列全体を count 回繰り返す
arr.cycle(2) { |e| print e }  # => "abcabc"

# 0 または負: ブロックが一度も実行されない
arr.cycle(0) { |e| print e }  # => 何も出力されない
arr.cycle(-5) { |e| print e } # => 何も出力されない

# nil または省略: 無限ループ（注意！）
# arr.cycle { |e| print e }  # => "abcabcabc..." 永遠に続く
```

### ユースケース

#### 1. ラウンドロビン方式の実装

```ruby
# サーバーの負荷分散
servers = ['server1', 'server2', 'server3']
server_cycle = servers.cycle

10.times do |i|
  server = server_cycle.next
  puts "Request #{i} → #{server}"
end
# Request 0 → server1
# Request 1 → server2
# Request 2 → server3
# Request 3 → server1
# ...
```

#### 2. 繰り返しパターンの生成

```ruby
# 交互に色を変えるテーブルの行
colors = ['white', 'lightgray']
rows = 10

rows.times.zip(colors.cycle) do |i, color|
  puts "Row #{i}: background-color: #{color}"
end
```

#### 3. 有限回数の繰り返し

```ruby
# アニメーションを3回再生
frames = ['frame1.png', 'frame2.png', 'frame3.png']
frames.cycle(3) do |frame|
  display(frame)
  sleep(0.1)
end
```

### 注意点

#### ⚠️ 無限ループの危険性

```ruby
# これは永遠に終わらない！
[1, 2, 3].cycle { |n| puts n }

# 必ず終了条件を設ける
[1, 2, 3].cycle do |n|
  puts n
  break if some_condition  # 終了条件が必要
end
```

#### ⚠️ メモリ効率

`cycle` は配列の要素を再利用するため、メモリ効率が良いです：

```ruby
# これはメモリを大量に消費しない
small_array = [1, 2, 3]
small_array.cycle(1_000_000) { |n| process(n) }

# 以下と比較すると効率的
# (small_array * 1_000_000).each { |n| process(n) }  # 巨大な配列を生成
```

### 関連メソッドとの比較

#### `Array#*` との違い

```ruby
# Array#* は新しい配列を生成する
arr = [1, 2]
repeated = arr * 3  # => [1, 2, 1, 2, 1, 2] (新しい配列)

# cycle はメモリを節約できる
arr.cycle(3) { |n| puts n }  # 元の配列を再利用
```

#### `Enumerator#take` との組み合わせ

```ruby
# 無限サイクルから有限個を取得
[1, 2, 3].cycle.take(7)  # => [1, 2, 3, 1, 2, 3, 1]

# first も使える
[1, 2, 3].cycle.first(5)  # => [1, 2, 3, 1, 2]
```

#### `loop` との比較

```ruby
# cycle を使った書き方
arr = ['a', 'b', 'c']
enum = arr.cycle
5.times { puts enum.next }

# loop を使った同等の処理
i = 0
5.times do
  puts arr[i % arr.size]
  i += 1
end
```

`cycle` の方が意図が明確で読みやすくなります。

### パフォーマンスの考慮事項

- **時間計算量**: O(n × count) - n は配列のサイズ
- **空間計算量**: O(1) - 新しい配列を生成しない
- 大量の繰り返しが必要な場合、`cycle` は効率的な選択肢

### まとめ

- `cycle` は配列を繰り返し処理する際に便利
- `count` で繰り返し回数を制御（省略すると無限ループ）
- ブロックなしで `Enumerator` を返すため、遅延評価が可能
- メモリ効率が良く、ラウンドロビンやパターン生成に最適
- **無限ループに注意**し、必ず終了条件を設けること
