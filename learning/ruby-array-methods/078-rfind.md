# Array#rfind

- 日付: 2026-03-19
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-rfind
- daily 記録: [daily/2026-03-19.md](../../daily/2026-03-19.md)

## 基本情報

### メソッドシグネチャ

```ruby
rfind(if_none_proc = nil) {|element| ... } → object or nil
rfind(if_none_proc = nil) → enumerator
```

### 概要

`rfind` は "reverse find" の略で、配列を**後ろから探索**して、ブロックが真値を返す最初の要素を返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

reverse find の略。配列を後ろから探索して、最初に条件に合う要素を返す

### コード例

```ruby
# rfind(if_none_proc = nil) {|element| ... } → object or nil
# rfind(if_none_proc = nil) → enumerator
# Returns the last element for which the block returns a truthy value.

# With a block given, calls the block with successive elements of the array in reverse order;
# returns the first element for which the block returns a truthy value:

[1, 2, 3, 4, 5, 6].rfind {|element| element < 5}       # => 4

# If no such element is found, calls if_none_proc and returns its return value.

[1, 2, 3, 4].rfind(proc {0}) {|element| element < -2}  # => 0

# With no block given, returns an Enumerator.
```

## 深掘り・補足

### 重要なポイント

#### 1. "reverse" の意味

`rfind` の "r" は reverse を意味し、配列の**末尾から先頭に向かって**探索します。これは `find` メソッドと対照的です：

```ruby
# find は前から探索
[1, 2, 3, 4, 5, 6].find {|element| element < 5}   # => 1（最初に見つかる）

# rfind は後ろから探索
[1, 2, 3, 4, 5, 6].rfind {|element| element < 5}  # => 4（後ろから最初に見つかる）
```

#### 2. `if_none_proc` パラメータ

条件に合う要素が見つからなかった場合の処理を指定できます：

```ruby
# if_none_proc なし（デフォルト）
[1, 2, 3, 4].rfind {|element| element > 10}  # => nil

# if_none_proc あり
[1, 2, 3, 4].rfind(proc { -1 }) {|element| element > 10}  # => -1
[1, 2, 3, 4].rfind(-> { "not found" }) {|element| element > 10}  # => "not found"
```

#### 3. ブロックなしの場合

ブロックを渡さない場合は Enumerator を返します：

```ruby
enum = [1, 2, 3, 4, 5].rfind
enum.class  # => Enumerator

# 後でブロックを渡すことができる
enum.find {|element| element.even?}  # => 4
```

### ユースケース

#### 1. 最後に条件を満たす要素を探す

```ruby
# ログの最後のエラーを見つける
logs = ["INFO: start", "DEBUG: processing", "ERROR: failed", "INFO: retry", "ERROR: timeout"]
last_error = logs.rfind {|log| log.start_with?("ERROR:")}  # => "ERROR: timeout"
```

#### 2. 配列の末尾から最初の有効な値を取得

```ruby
# 設定値の優先順位（後ろほど優先度が高い）
settings = [nil, "default", nil, "user_preference", nil]
active_setting = settings.rfind {|s| !s.nil?}  # => "user_preference"
```

#### 3. 条件に合う最新のアイテムを探す

```ruby
# 最新の完了したタスクを探す
tasks = [
  {id: 1, status: "pending"},
  {id: 2, status: "completed"},
  {id: 3, status: "pending"},
  {id: 4, status: "completed"}
]
last_completed = tasks.rfind {|task| task[:status] == "completed"}
# => {id: 4, status: "completed"}
```

### 注意点

#### 1. `find_last` との関係

`rfind` は `find_last` のエイリアスです。`find_last` は Ruby 2.7 で導入されたメソッドで、`rfind` はそのエイリアスとして提供されています。

```ruby
# 以下は同じ動作
[1, 2, 3, 4, 5].find_last {|x| x.even?}  # => 4
[1, 2, 3, 4, 5].rfind {|x| x.even?}      # => 4
```

古いバージョンの Ruby（2.7 未満）との互換性を考慮する必要がある場合は、`reverse.find` を使用するのが安全です。

#### 2. パフォーマンス

配列全体を逆順に走査するため、大きな配列で条件に合う要素が先頭付近にある場合、効率が悪くなります。

#### 3. `reverse.find` との違い

以下の2つは似ていますが、微妙に異なります：

```ruby
# rfind：配列を逆順に走査するが、配列自体は変更しない
[1, 2, 3, 4, 5].rfind {|x| x < 4}  # => 3

# reverse.find：配列を逆順にしてから find を実行
[1, 2, 3, 4, 5].reverse.find {|x| x < 4}  # => 3
```

結果は同じですが、`rfind` の方がメモリ効率が良いです（新しい配列を作成しないため）。

### 関連メソッドとの比較

| メソッド | 探索方向 | 返り値 |
|---------|---------|--------|
| `find` | 前から | 最初に見つかった要素 |
| `rfind` | 後ろから | 最初に見つかった要素（= 配列上は最後） |
| `find_index` | 前から | 最初に見つかった要素のインデックス |
| `rindex` | 後ろから | 最初に見つかった要素のインデックス |
| `select` | 前から | 条件に合う全ての要素 |
