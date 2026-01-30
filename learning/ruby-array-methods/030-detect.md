# Array#detect

- 日付: 2026-01-30
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-detect
  - find: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-find

## 基本情報

`detect` は `find` のエイリアスメソッドです。Enumerable モジュールから提供されるメソッドで、配列の要素を順に調べて、ブロックが真値を返す最初の要素を返します。

**メソッドシグネチャ**:
```ruby
detect(if_none_proc = nil) { |element| ... } → object or nil
detect(if_none_proc = nil) → Enumerator
```

## daily からの記録

### メモ

detect、何回かみて覚えられてなかったので出てきてありがたい

### コード例・補足

```ruby
# Returns the first element for which the block returns a truthy value.
# ブロックが真値を返す最初の要素を返します。
# With a block given, calls the block with successive elements of the array; returns the first element for which the block returns a truthy value:
# ブロックが与えられた場合、配列の連続する要素を引数としてブロックを呼び出し、ブロックが真値を返した最初の要素を返します。
[1, 3, 5].find {|element| element > 2} # => 3

# If no such element is found, calls if_none_proc and returns its return value.
# もし該当する要素が見つからなかった場合、`if_none_proc`が呼び出され、その戻り値が返されます。
[1, 3, 5].find {|element| element > 6} # => nil
[1, 3, 5].find(proc{-1}) {|element| element > 6} # => -1

# With no block given, returns an Enumerator.
# ブロックが与えられない場合、Enumeratorを返します。
[1, 3, 5].find # => #<Enumerator: ...>
```

## 深掘り・補足

### 重要なポイント

1. **最初の一致を返す**
   - 条件を満たす要素が複数あっても、**最初の1つ**だけを返す
   - すべての要素を調べるのではなく、見つかった時点で探索を終了（効率的）

2. **find のエイリアス**
   - `detect` と `find` は完全に同じ動作
   - Ruby コミュニティでは `find` の方がよく使われる傾向

3. **デフォルト値の指定**
   - `if_none_proc` を指定することで、見つからなかった場合の戻り値をカスタマイズ可能
   - Proc オブジェクトを渡す必要がある（`proc { ... }` または `lambda { ... }`）

### ユースケース

```ruby
# ユーザーを ID で検索
users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 3, name: 'Charlie' }
]
user = users.detect { |u| u[:id] == 2 }
# => { id: 2, name: 'Bob' }

# 偶数を見つける
numbers = [1, 3, 7, 8, 9, 10]
first_even = numbers.detect(&:even?)
# => 8

# デフォルト値を設定
result = [1, 3, 5].detect(proc { "not found" }) { |n| n > 10 }
# => "not found"
```

### 注意点

1. **パフォーマンス**
   - 大きな配列で条件を満たす要素が後ろの方にある場合、その手前まですべて調べる
   - ソート済み配列なら `bsearch` の方が高速な場合もある

2. **nil と false**
   - 見つからなかった場合は `nil` を返す
   - 配列に `nil` が含まれている場合、「見つからなかった」のか「nil が見つかった」のか区別できない
   ```ruby
   [nil, 1, 2].detect { |n| n.nil? } # => nil（見つかった）
   [1, 2, 3].detect { |n| n.nil? }   # => nil（見つからなかった）
   ```

3. **破壊的変更ではない**
   - 元の配列は変更されない
   - 要素を見つけるだけで、配列から削除したりはしない

### 関連メソッドとの比較

| メソッド | 戻り値 | 用途 |
|---------|-------|------|
| `detect` / `find` | 最初の1要素 | 条件を満たす要素を1つだけ取得 |
| `select` / `filter` | 配列 | 条件を満たすすべての要素を取得 |
| `find_all` | 配列 | `select` のエイリアス |
| `find_index` | インデックス（整数） | 条件を満たす要素の位置を取得 |
| `any?` | true/false | 条件を満たす要素が存在するか確認 |

### 実践的なヒント

- **存在確認には `any?` を使う**: 要素自体が不要で、存在だけ確認したい場合
  ```ruby
  # 良くない
  if users.detect { |u| u[:admin] }
    # ...
  end

  # 良い
  if users.any? { |u| u[:admin] }
    # ...
  end
  ```

- **複数条件は && でつなぐ**:
  ```ruby
  users.detect { |u| u[:active] && u[:role] == 'admin' }
  ```

- **Symbol to_proc を活用**:
  ```ruby
  [1, nil, 3, nil, 5].detect(&:nil?)  # => nil（最初の nil を見つける）
  ```
