# Array#last

- 日付: 2026-02-27
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-last
- daily 記録: [daily/2026-02-27.md](../../daily/2026-02-27.md)

## 基本情報

```ruby
last → object or nil
last(count) → new_array
```

配列の末尾の要素を返します。配列自体は変更されません。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

（メモなし）

### コード例

```ruby
# Returns elements from self, or nil; self is not modified.
# 自身の要素を返す（またはnil）。自身は変更されない。
# With no argument given, returns the last element, or nil if self is empty:
# 引数が指定されない場合、最後の要素を返します。自身が空であればnilを返します。
a = [:foo, 'bar', 2]
a.last # => 2
a # => [:foo, "bar", 2]
[].last # => nil

# With non-negative integer argument count given, returns a new array containing the trailing count elements of self, as available:
# 負ではない整数`count`が与えられた場合、自身の末尾の`count`個の要素を（利用可能な範囲で）含む新しい配列を返します。
a = [:foo, 'bar', 2]
a.last(2)  # => ["bar", 2]
a.last(50) # => [:foo, "bar", 2]
a.last(0)  # => []
[].last(3) # => []
```

## 深掘り・補足

### 重要なポイント

#### 1. 非破壊的な操作

`last` は配列を変更しません：

```ruby
a = [1, 2, 3, 4, 5]
a.last      # => 5
a.last(3)   # => [3, 4, 5]
a           # => [1, 2, 3, 4, 5] (変更なし)
```

これは `pop` とは対照的です：

```ruby
a = [1, 2, 3, 4, 5]
a.pop       # => 5
a           # => [1, 2, 3, 4] (末尾要素が削除される)
```

#### 2. 引数なしと引数ありで戻り値の型が異なる

- **引数なし**: 単一の要素（またはnil）を返す
- **引数あり**: 常に新しい配列を返す

```ruby
a = [1, 2, 3]
a.last      # => 3 (Integer)
a.last(1)   # => [3] (Array)
```

#### 3. 空配列の扱い

空配列に対しても安全に使用できます：

```ruby
[].last     # => nil
[].last(5)  # => []
```

### ユースケース

#### 最新のデータを取得

```ruby
# ログの最新エントリを確認
logs = ["Error 1", "Warning 1", "Error 2"]
latest_log = logs.last  # => "Error 2"

# 直近3件を取得
recent_logs = logs.last(3)  # => ["Error 1", "Warning 1", "Error 2"]
```

#### データの末尾を確認（非破壊的に）

```ruby
# 配列を保持したまま末尾をチェック
queue = ["task1", "task2", "task3"]
last_task = queue.last  # => "task3"
# queue はそのまま ["task1", "task2", "task3"]
```

### 注意点

#### 1. 引数が配列のサイズを超える場合

要求された数より要素が少ない場合、利用可能な要素すべてを返します：

```ruby
a = [1, 2, 3]
a.last(10)  # => [1, 2, 3] (エラーにならない)
```

#### 2. 負の引数

負の整数を渡すとエラーになります：

```ruby
a = [1, 2, 3]
a.last(-1)  # ArgumentError (negative array size)
```

#### 3. 単一要素の配列

1要素の配列でも、引数の有無で戻り値が異なります：

```ruby
a = [100]
a.last      # => 100 (Integer)
a.last(1)   # => [100] (Array)
```

### 関連メソッドとの比較

| メソッド | 動作 | 破壊的 | 戻り値 |
|---------|------|--------|--------|
| `last` | 末尾要素を取得 | × | 要素 or 配列 |
| `first` | 先頭要素を取得 | × | 要素 or 配列 |
| `pop` | 末尾要素を削除＆取得 | ○ | 要素 or 配列 |
| `take` | 先頭から n 個取得 | × | 配列 |
| `drop` | 先頭 n 個を除いた残り | × | 配列 |

### パフォーマンスの考慮事項

`last` は配列の末尾にアクセスするため、配列のサイズに関わらず高速です（O(1)）：

```ruby
small_array = [1, 2, 3]
huge_array = (1..1_000_000).to_a

small_array.last  # 高速
huge_array.last   # 同じく高速（配列のサイズに影響されない）
```

### 実践例：ページネーション

```ruby
# 全データ
all_items = (1..100).to_a

# 最後のページ（10件表示）
last_page = all_items.last(10)
# => [91, 92, 93, 94, 95, 96, 97, 98, 99, 100]

# 最後のアイテムを確認
last_item = all_items.last  # => 100
```
