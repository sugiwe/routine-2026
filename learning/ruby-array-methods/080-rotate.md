# Array#rotate

- 日付: 2026-03-21
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-rotate
- daily 記録: [daily/2026-03-21.md](../../daily/2026-03-21.md)

## 基本情報

```ruby
rotate(count = 1) → new_array
```

配列の要素を一方の端からもう一方の端へ回転させた新しい配列を返します。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# Returns a new array formed from self with elements rotated from one end to the other.
# 配列の要素を一方の端からもう一方の端へ回転させ、新しい配列として返します。
# With non-negative numeric count, rotates elements from the beginning to the end:
# 0以上の数値を指定すると、先頭の要素から順に末尾へ移動させます。
[0, 1, 2, 3].rotate(2)   # => [2, 3, 0, 1]
[0, 1, 2, 3].rotate(2.1) # => [2, 3, 0, 1]

# If count is large, uses count % array.size as the count:
# count が大きい場合、count % array.size を count として使用します。
[0, 1, 2, 3].rotate(22) # => [2, 3, 0, 1]

# With a count of zero, rotates no elements:
# カウントがゼロの場合、要素は回転しません。
[0, 1, 2, 3].rotate(0) # => [0, 1, 2, 3]

# With negative numeric count, rotates in the opposite direction, from the end to the beginning:
# 数値に負の値を指定すると、末尾から先頭に向かって逆方向に回転します。
[0, 1, 2, 3].rotate(-1) # => [3, 0, 1, 2]

# If count is small (far from zero), uses count % array.size as the count:
# カウントが小さい場合（0から離れている場合）、count % array.size をカウントとして使用します。
[0, 1, 2, 3].rotate(-21) # => [3, 0, 1, 2]

# Related: see Methods for Fetching.
```

## 深掘り・補足

### 重要なポイント

#### 非破壊的操作

`rotate` は元の配列を変更せず、新しい配列を返します。元の配列を直接変更したい場合は `rotate!` を使用します。

```ruby
arr = [1, 2, 3, 4, 5]
arr.rotate(2)  # => [3, 4, 5, 1, 2]
arr            # => [1, 2, 3, 4, 5] （変更されていない）

arr.rotate!(2) # => [3, 4, 5, 1, 2]
arr            # => [3, 4, 5, 1, 2] （変更された）
```

#### 引数の省略

引数を省略すると、デフォルトで1要素だけ回転します。

```ruby
[1, 2, 3, 4, 5].rotate  # => [2, 3, 4, 5, 1]
```

#### モジュロ演算の自動適用

配列のサイズを超える数値を指定した場合、自動的に `count % array.size` が適用されます。これにより、無駄な回転を避けて効率的に処理されます。

```ruby
arr = [1, 2, 3, 4]
arr.rotate(10)  # => [3, 4, 1, 2]  (10 % 4 = 2と同じ結果)
```

### ユースケース

#### 1. ラウンドロビンスケジューリング

タスクやリソースを順番に割り当てる際に便利です。

```ruby
servers = ['server1', 'server2', 'server3']
current_server = servers.first  # 'server1'
servers = servers.rotate        # ['server2', 'server3', 'server1']
```

#### 2. リストの循環処理

カルーセルやスライドショーのような循環表示を実装できます。

```ruby
slides = ['slide1', 'slide2', 'slide3']
slides = slides.rotate  # 次のスライドへ
```

#### 3. キューの実装

先頭要素を末尾に移動させることで、簡易的なキューを実装できます。

```ruby
queue = [1, 2, 3, 4]
queue = queue.rotate  # [2, 3, 4, 1]
```

### 注意点

#### パフォーマンス

`rotate` は新しい配列を生成するため、大きな配列では `rotate!` の方がメモリ効率が良い場合があります。

#### 破壊的メソッドとの使い分け

- 元の配列を保持したい → `rotate`
- メモリ節約、元の配列が不要 → `rotate!`

### 関連メソッドとの比較

| メソッド | 機能 | 破壊的 | 用途 |
|---------|------|------|------|
| `rotate` | 要素を回転 | いいえ | 循環処理 |
| `rotate!` | 要素を回転 | はい | メモリ節約 |
| `reverse` | 順序を反転 | いいえ | 逆順 |
| `reverse!` | 順序を反転 | はい | メモリ節約 |
| `shift` | 先頭要素を削除・取得 | はい | キュー操作 |
| `unshift` | 先頭に要素を追加 | はい | キュー操作 |

### 実践的な例

```ruby
# ローテーション表示の実装
team_members = ['Alice', 'Bob', 'Charlie', 'David']
current_leader = team_members.first
puts "今週のリーダー: #{current_leader}"
team_members = team_members.rotate  # 次週のためにローテーション

# 配列の一部を先頭に持ってくる
tasks = ['task1', 'task2', 'task3', 'task4']
tasks.rotate(2)  # => ['task3', 'task4', 'task1', 'task2']
# task3を先頭にしたい場合に便利
```
