# Array#prepend

- 日付: 2026-03-09
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-prepend
- daily 記録: [daily/2026-03-09.md](../../daily/2026-03-09.md)

## 基本情報

```ruby
prepend(*objects) → self
```

`Array#prepend` は配列の先頭に指定されたオブジェクトを追加するメソッドです。`unshift` のエイリアスとして提供されています。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# Prepends the given objects to self:
# 指定されたオブジェクトを自身の先頭に追加します:
a = [:foo, 'bar', 2]
a.prepend(:bam, :bat) # => [:bam, :bat, :foo, "bar", 2]

# Related: Array#shift; see also Methods for Assigning.
# Alias for: unshift
```

## 深掘り・補足

### メソッドの特徴

1. **unshift のエイリアス**
   - `prepend` と `unshift` は完全に同じ動作をします
   - どちらを使っても機能は同一

2. **破壊的メソッド**
   - 元の配列を直接修正します（非破壊的バージョンはありません）
   - 戻り値は修正後の配列自身（`self`）

3. **複数要素の追加**
   - 複数の要素を一度に先頭に追加できます
   - 引数の順序通りに先頭に挿入されます

### 使用例

```ruby
# 単一要素の追加
arr = [2, 3, 4]
arr.prepend(1)        # => [1, 2, 3, 4]

# 複数要素の追加
arr = [3, 4]
arr.prepend(1, 2)     # => [1, 2, 3, 4]

# 配列を展開せずに追加
arr = [:b, :c]
arr.prepend([:a])     # => [[:a], :b, :c]

# 配列を展開して追加したい場合は * を使用
arr = [:b, :c]
arr.prepend(*[:a])    # => [:a, :b, :c]
```

### prepend vs unshift の使い分け

どちらも同じ機能ですが、コードの意図を明確にする観点から使い分けることができます：

- **`prepend`**: 「先頭に追加する」という意図が明確
  - より現代的で読みやすい
  - append との対称性がある（append ↔ prepend）

- **`unshift`**: 伝統的な Ruby のメソッド名
  - shift の逆操作であることが明確
  - 古いコードベースでよく見られる

```ruby
# 意図が明確な例
queue = []
queue.append(:task1)    # 末尾に追加
queue.prepend(:urgent)  # 先頭に追加（優先タスク）

# shift/unshift のペアで考える例
stack = [2, 3]
stack.unshift(1)        # 先頭に追加
first = stack.shift     # 先頭から取り出し
```

### 関連メソッドとの比較

| メソッド  | 機能               | 位置     | エイリアス |
| --------- | ------------------ | -------- | ---------- |
| `prepend` | 追加（破壊的）     | 先頭     | `unshift`  |
| `append`  | 追加（破壊的）     | 末尾     | `push`     |
| `shift`   | 取り出し（破壊的） | 先頭     | -          |
| `pop`     | 取り出し（破壊的） | 末尾     | -          |
| `insert`  | 任意位置に挿入     | 指定位置 | -          |

### パフォーマンスの考慮事項

- 配列の先頭への挿入は、内部的に全要素をシフトする必要があるため、**O(n)** の時間がかかります
- 頻繁に先頭への追加を行う場合は、代わりにリンクリストのようなデータ構造の使用を検討してください
- 末尾への追加（`append`/`push`）は **O(1)** で高速です

### 実用例：優先度付きキュー

```ruby
# 優先度付きタスクキュー
tasks = [:normal_task1, :normal_task2]

# 通常タスクは末尾に追加
tasks.append(:normal_task3)  # => [:normal_task1, :normal_task2, :normal_task3]

# 緊急タスクは先頭に追加
tasks.prepend(:urgent_task)  # => [:urgent_task, :normal_task1, :normal_task2, :normal_task3]

# 処理は先頭から
next_task = tasks.shift      # => :urgent_task
```

### 関連メソッド

- [005. <<](005-left-shift.md) - 末尾に要素を追加
- [013. append](013-append.md) - 末尾に要素を追加（push のエイリアス）
- [067. pop](067-pop.md) - 末尾から要素を取り出し
- [052. insert](052-insert.md) - 任意の位置に要素を挿入
