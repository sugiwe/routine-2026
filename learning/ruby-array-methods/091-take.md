# Array#take

- 日付: 2026-04-01
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-take
- daily 記録: [daily/2026-04-01.md](../../daily/2026-04-01.md)

## 基本情報

```ruby
take(count) → new_array
```

配列の**先頭から指定された個数の要素**を含む新しい配列を返します。元の配列（`self`）は変更されません。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# Returns a new array containing the first count element of self (as available); count must be a non-negative numeric; does not modify self:
# self の最初の count 個の要素（利用可能な場合）を含む新しい配列を返します。count は非負の数値でなければなりません。self 自体は変更されません：
a = ['a', 'b', 'c', 'd']
a.take(2)   # => ["a", "b"]
a.take(2.1) # => ["a", "b"]
a.take(50)  # => ["a", "b", "c", "d"]
a.take(0)   # => []

# Related: see Methods for Fetching.
```

## 深掘り・補足

### 重要なポイント

1. **非破壊的メソッド**: 元の配列は変更されず、新しい配列を返す
2. **引数は非負の数値**: 負の値は指定できない（ArgumentError になる）
3. **浮動小数点数も受け付ける**: `2.1` → `2` のように整数に変換される
4. **count が配列長を超えても安全**: エラーにならず、全要素を返す

### エッジケース

```ruby
arr = [1, 2, 3]

# count が配列長を超える場合
arr.take(10)  # => [1, 2, 3]  # エラーにならず、全要素を返す

# count が 0 の場合
arr.take(0)   # => []  # 空配列を返す

# 浮動小数点数の場合
arr.take(2.9) # => [1, 2]  # 整数部分のみ使用

# 負の値（エラー）
arr.take(-1)  # ArgumentError: attempt to take negative size
```

### 関連メソッドとの比較

| メソッド | 動作 | 例 |
|---------|------|-----|
| `take(n)` | 先頭から n 個を取得 | `[1,2,3,4].take(2)` → `[1,2]` |
| `first(n)` | 先頭から n 個を取得（`take` とほぼ同じ） | `[1,2,3,4].first(2)` → `[1,2]` |
| `drop(n)` | 先頭 n 個を**スキップ**して残りを返す | `[1,2,3,4].drop(2)` → `[3,4]` |
| `slice(0, n)` | 先頭から n 個を取得（範囲指定版） | `[1,2,3,4].slice(0, 2)` → `[1,2]` |
| `[0...n]` | 先頭から n 個を取得（ブラケット記法） | `[1,2,3,4][0...2]` → `[1,2]` |

**`take` vs `first`**:
- 動作はほぼ同じだが、`first` は引数なしで先頭1要素を返すこともできる
- `take` は必ず配列を返す（引数が必須）
- `first` は引数なしだと単一の要素を返す

```ruby
arr = [1, 2, 3]
arr.first      # => 1  # 単一要素
arr.first(2)   # => [1, 2]  # 配列
arr.take(2)    # => [1, 2]  # 配列
```

**`take` と `drop` の相補関係**:

```ruby
arr = [1, 2, 3, 4, 5, 6]
arr.take(3)  # => [1, 2, 3]
arr.drop(3)  # => [4, 5, 6]

# 両者を組み合わせると元の配列を再構成できる
arr.take(3) + arr.drop(3)  # => [1, 2, 3, 4, 5, 6]
```

### 実践的な使用例

**ページネーション**:
```ruby
items = (1..100).to_a

# 最初の10件を表示
page_1 = items.take(10)  # => [1, 2, ..., 10]

# 実際のページネーションでは drop と組み合わせる
page_2 = items.drop(10).take(10)  # => [11, 12, ..., 20]
```

**プレビュー表示**:
```ruby
articles = Article.all

# 最新3件のプレビュー
preview = articles.take(3)
```

**安全な配列アクセス**:
```ruby
# count が配列長を超えてもエラーにならない
user_input = 100  # ユーザーからの入力
items = [1, 2, 3]
safe_items = items.take(user_input)  # => [1, 2, 3]  # エラーにならない
```

### パフォーマンスの考慮事項

- `take` は新しい配列を生成するため、メモリを消費する
- 大きな配列から少数の要素を取得する場合でも、新しい配列が作られる
- Lazy Enumerator と組み合わせると効率的：

```ruby
# 無限のシーケンスから最初の5つを取得
(1..Float::INFINITY).lazy.select(&:even?).take(5).to_a
# => [2, 4, 6, 8, 10]
```

**なぜ `.lazy` が必要か**:
- `.lazy` がないと、`select(&:even?)` が無限ループになる（無限の範囲全体に対して評価しようとするため）
- 遅延評価により、`take(5)` が呼ばれるまで `select` の実行が保留される
- 必要な5つの偶数が見つかった時点で処理が終了するため、効率的に動作する

```ruby
# NG: 無限ループになる
# (1..Float::INFINITY).select(&:even?).take(5)

# OK: 遅延評価で効率的に動作
(1..Float::INFINITY).lazy.select(&:even?).take(5).to_a
```

### まとめ

`Array#take` は、配列の先頭から指定された個数の要素を安全に取得するための便利なメソッドです。非破壊的で、count が配列長を超えてもエラーにならないため、安心して使えます。`drop` との相補関係や、`first`/`slice` との使い分けを理解しておくと、より効果的なコードが書けます。
