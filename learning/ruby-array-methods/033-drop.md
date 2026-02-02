# Array#drop

- 日付: 2026-02-02
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-drop
- daily 記録: [daily/2026-02-02.md](../../daily/2026-02-02.md)

## 基本情報

```ruby
drop(count) → new_array
```

配列の先頭から指定された個数（count）の要素を除いた新しい配列を返します。元の配列（self）は変更されません。count は負ではない整数である必要があります。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

`drop(count) → new_array`

### コード例

```ruby
# Returns a new array containing all but the first count element of self, where count is a non-negative integer; does not modify self.
# 指定された要素数（count）を先頭から除いた新しい配列を返します。元の配列（self）は変更されません。countは負ではない整数である必要があります。

# Examples:
a = [0, 1, 2, 3, 4, 5]
a.drop(0) # => [0, 1, 2, 3, 4, 5]
a.drop(1) # => [1, 2, 3, 4, 5]
a.drop(2) # => [2, 3, 4, 5]
a.drop(9) # => []
```

## 深掘り・補足

### 重要なポイント

1. **非破壊的メソッド**
   - 元の配列は変更されません
   - 新しい配列が返されます
   - メモリ効率を考慮する必要がある場合は注意が必要

2. **境界値の処理**
   - count が 0 の場合：元の配列のコピーが返される
   - count が配列の長さ以上の場合：空配列 `[]` が返される
   - count が負の値の場合：`ArgumentError` が発生

### 実践的なユースケース

#### 1. ヘッダー行のスキップ

CSV データなどで、最初の行（ヘッダー行）をスキップしたい場合：

```ruby
csv_data = [
  ['Name', 'Age', 'Email'],
  ['Alice', 30, 'alice@example.com'],
  ['Bob', 25, 'bob@example.com']
]

data_rows = csv_data.drop(1)
# => [['Alice', 30, 'alice@example.com'], ['Bob', 25, 'bob@example.com']]
```

#### 2. ページネーション

特定のページの開始位置から配列を取得する場合：

```ruby
items = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
page_size = 3
page_number = 2  # 2ページ目

page_items = items.drop(page_size * (page_number - 1)).take(page_size)
# => ['d', 'e', 'f']
```

#### 3. 最初のN件を除外してログ解析

```ruby
log_entries = [
  'INIT: Starting application',
  'INFO: Loading config',
  'INFO: Connecting to database',
  'ERROR: Connection timeout',
  'WARN: Retrying connection'
]

# 最初の2件（初期化ログ）をスキップ
important_logs = log_entries.drop(2)
# => ['INFO: Connecting to database', 'ERROR: Connection timeout', 'WARN: Retrying connection']
```

### 関連メソッドとの比較

| メソッド | 動作 | 元の配列への影響 | 戻り値 |
|---------|------|----------------|--------|
| `drop(n)` | 先頭n個を除外 | 変更しない | 残りの要素を含む新配列 |
| `drop_while {block}` | 条件を満たす間除外 | 変更しない | 条件外の要素以降の新配列 |
| `shift` | 先頭要素を削除 | **変更する** | 削除した要素 |
| `shift(n)` | 先頭n個を削除 | **変更する** | 削除した要素の配列 |
| `first(n)` | 先頭n個を取得 | 変更しない | 先頭n個の要素 |
| `take(n)` | 先頭n個を取得 | 変更しない | 先頭n個の要素 |

### drop と take の相補関係

`drop` と `take` は対になるメソッドで、配列を二分割できます：

```ruby
arr = [1, 2, 3, 4, 5]
n = 2

first_part = arr.take(n)   # => [1, 2]
second_part = arr.drop(n)  # => [3, 4, 5]

# first_part + second_part == arr
```

### パフォーマンスの考慮事項

- `drop` は新しい配列を作成するため、大きな配列では メモリを消費します
- 元の配列を変更しても良い場合は、`shift(n)` の方がメモリ効率が良い
- イテレータを使う場合は `each_with_index` でスキップする方が効率的な場合もあります：

```ruby
# drop を使う場合
array.drop(100).each { |item| process(item) }

# 効率的な代替案（大規模配列の場合）
array.each_with_index { |item, i| process(item) if i >= 100 }
```

### 注意点

1. **負の値はエラーになる**
   ```ruby
   [1, 2, 3].drop(-1)  # ArgumentError: attempt to drop negative size
   ```

2. **元の配列を変更したい場合は slice! を使う**
   ```ruby
   arr = [1, 2, 3, 4, 5]
   arr.slice!(0, 2)  # => [1, 2] を返し、arr は [3, 4, 5] になる
   ```

3. **空配列に対しても安全**
   ```ruby
   [].drop(5)  # => []（エラーにならない）
   ```
