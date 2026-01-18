# Array#clear

- 日付: 2026-01-18
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-clear

## 基本情報

```ruby
clear → self
```

配列のすべての要素を削除し、空になった配列自身を返します。

## daily からの記録

### メモ

`clear → self`

### コード例・補足

```ruby
# Removes all elements from self; returns self:
# すべて要素を削除します。自身を返します。
a = [:foo, 'bar', 2]
a.clear # => []
```

## 深掘り・補足

### 重要なポイント

1. **破壊的メソッド**: 元の配列を直接変更します
2. **戻り値は self**: 空になった配列自身を返すため、メソッドチェーンが可能
3. **完全な削除**: すべての要素が削除されるため、元の内容は復元できません

### 使用例

```ruby
# 基本的な使い方
arr = [1, 2, 3, 4, 5]
arr.clear  # => []
arr        # => []

# メソッドチェーン
result = [1, 2, 3].clear.push(10, 20)
# => [10, 20]

# 条件付きクリア
data = [1, 2, 3]
data.clear if data.size > 5  # 条件に合わないので変更なし
data  # => [1, 2, 3]
```

### 他の方法との比較

```ruby
arr = [1, 2, 3]

# clear を使う（破壊的）
arr.clear
# arr => []

# 新しい配列を代入（非破壊的、参照が変わる）
arr = []

# replace を使う（破壊的）
arr.replace([])
```

### 注意点

#### オブジェクト参照との関係

```ruby
# 同じ配列を参照している場合
original = [1, 2, 3]
reference = original

original.clear
puts original   # => []
puts reference  # => [] （同じオブジェクトなので影響を受ける）

# 代入の場合
original = [1, 2, 3]
reference = original

original = []
puts original   # => []
puts reference  # => [1, 2, 3] （参照が変わっただけなので影響なし）
```

#### freeze された配列

```ruby
arr = [1, 2, 3].freeze
arr.clear  # FrozenError: can't modify frozen Array
```

### ユースケース

1. **配列の再利用**: 同じ配列オブジェクトを保持したまま内容をリセット
2. **メモリ管理**: 大きな配列の内容を削除してメモリを解放
3. **ループ内での初期化**: 繰り返し処理で配列をクリアして再利用

```ruby
# 配列の再利用例
buffer = []

5.times do |i|
  buffer.push(i, i * 2)
  puts "Iteration #{i}: #{buffer.inspect}"
  buffer.clear  # 次の繰り返しのためにクリア
end
```

### パフォーマンスの考慮

- `clear` は既存の配列をそのまま使うため、新しい配列を作るよりメモリ効率が良い
- 配列のサイズが非常に大きい場合でも高速に動作する

### 関連メソッド

- `replace([])`: 同じ効果だが、より明示的
- `delete_if { true }`: すべての要素を削除（条件付き）
- `shift` / `pop`: 要素を1つずつ削除
