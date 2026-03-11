# Array#push

- 日付: 2026-03-11
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-push
- daily 記録: [daily/2026-03-11.md](../../daily/2026-03-11.md)

## 基本情報

```ruby
push(*objects) → self
```

**別名**: `append`

配列の末尾に要素を追加し、配列自身を返すメソッドです。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

pushとappendはエイリアス

### コード例

```ruby
# Appends each argument in objects to self; returns self:
# オブジェクト内の各引数を自身に追加し、自身を返します。
a = [:foo, 'bar', 2] # => [:foo, "bar", 2]
a.push(:baz, :bat)   # => [:foo, "bar", 2, :baz, :bat]

# Appends each argument as a single element, even if it is another array:
# 引数が別の配列であっても、それぞれを単一の要素として追加します。
a = [:foo, 'bar', 2]               # => [:foo, "bar", 2]
a.push([:baz, :bat], [:bam, :bad]) # => [:foo, "bar", 2, [:baz, :bat], [:bam, :bad]]

# Related: see Methods for Assigning.
# Also aliased as: append
```

## 深掘り・補足

### 重要なポイント

1. **破壊的メソッド**
   - 元の配列を変更します（non-destructive なメソッドではありません）
   - 戻り値は配列自身なので、メソッドチェーンが可能

2. **複数要素の追加が可能**
   - 引数を複数取ることができ、すべて末尾に順番に追加されます
   - `push(a, b, c)` のように複数指定可能

3. **配列を要素として追加**
   - 配列を引数に渡すと、その配列全体が**1つの要素**として追加されます
   - 配列を展開して追加したい場合は `concat` を使用します

### 関連メソッドとの比較

| メソッド | 動作 | 破壊的 | 複数要素 |
|---------|------|--------|---------|
| `push(*objects)` | 末尾に追加 | ✓ | ✓ |
| `append(*objects)` | push のエイリアス | ✓ | ✓ |
| `<<(object)` | 末尾に追加 | ✓ | 1つのみ |
| `concat(*other_arrays)` | 配列を展開して追加 | ✓ | ✓ |
| `+` | 配列を連結（非破壊的） | ✗ | - |

### 使い分けの例

```ruby
# push: 複数要素を追加
a = [1, 2, 3]
a.push(4, 5)  # => [1, 2, 3, 4, 5]

# <<: 1つの要素を追加（メソッドチェーン向き）
a = [1, 2, 3]
a << 4 << 5  # => [1, 2, 3, 4, 5]

# push で配列を追加: ネストされる
a = [1, 2, 3]
a.push([4, 5])  # => [1, 2, 3, [4, 5]]

# concat で配列を追加: 展開される
a = [1, 2, 3]
a.concat([4, 5])  # => [1, 2, 3, 4, 5]
```

### ユースケース

1. **スタック操作**
   ```ruby
   stack = []
   stack.push("item1")
   stack.push("item2")
   # => ["item1", "item2"]
   ```

2. **複数要素の一括追加**
   ```ruby
   tasks = ["task1", "task2"]
   tasks.push("task3", "task4", "task5")
   # => ["task1", "task2", "task3", "task4", "task5"]
   ```

3. **メソッドチェーン**
   ```ruby
   result = [].push(1, 2).push(3, 4)
   # => [1, 2, 3, 4]
   ```

### パフォーマンスの考慮事項

- 配列の末尾への追加は **O(1)** の操作で非常に高速です
- 大量の要素を追加する場合、`push(*many_elements)` は問題ありませんが、スプラット演算子のオーバーヘッドに注意
- 非常に大量の要素を追加する場合は、`concat` の方が効率的な場合があります

### append との関係

`push` と `append` は完全なエイリアスです。どちらを使っても同じ動作をします。

```ruby
a = [1, 2, 3]
a.push(4)    # => [1, 2, 3, 4]

b = [1, 2, 3]
b.append(4)  # => [1, 2, 3, 4]  # 完全に同じ
```

コードの可読性の観点から、好みの方を選んで使用できます。
