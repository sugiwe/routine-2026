# Array#==

- 日付: 2026-01-07
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-3D-3D

## 基本情報

```ruby
== (other_array) → true or false
```

2つの配列が等しいかどうかを判定します。`self` の各要素が `other_array` の対応する要素と `==` で比較されます。

## メモ（daily より）

`self == other_array → true or false`

- Returns whether both:
  - self and other_array are the same size.
  - Their corresponding elements are the same; that is, for each index i in (0...self.size), self[i] == other_array[i].
- self と other_array の両方が以下の条件を満たすかどうかを返します：
  - 両配列のサイズが同じであること。
  - 対応する要素が同一であること。つまり、(0...self.size) の各インデックス i について、self[i] == other_array[i] が成立すること。

```ruby
# Examples:
[:foo, 'bar', 2] == [:foo, 'bar', 2]   # => true
[:foo, 'bar', 2] == [:foo, 'bar', 2.0] # => true
[:foo, 'bar', 2] == [:foo, 'bar']      # => false # Different sizes.
[:foo, 'bar', 2] == [:foo, 'bar', 3]   # => false # Different elements.
```

## 深掘り・補足

### 重要なポイント

1. **サイズと要素の両方をチェック**
   - 配列のサイズが異なれば即座に `false`
   - サイズが同じでも、対応する要素が異なれば `false`

2. **要素ごとに `==` で比較**
   - 各要素の比較には `==` メソッドを使用
   - 数値の型変換など、`==` の寛容な比較が適用される
   - 例: `2 == 2.0` は `true`（Integer と Float の比較）

3. **順序も考慮される**
   - 同じ要素を持っていても、順序が異なれば `false`
   ```ruby
   [1, 2, 3] == [3, 2, 1]  # => false
   ```

### ユースケース

#### テストでの使用
```ruby
# RSpec などのテストでよく使われる
expect(result).to eq([1, 2, 3])
```

#### 配列の内容確認
```ruby
user_input = ['apple', 'banana', 'cherry']
expected = ['apple', 'banana', 'cherry']

if user_input == expected
  puts "正しい入力です"
end
```

#### データの検証
```ruby
config_a = { tags: ['ruby', 'rails'] }
config_b = { tags: ['ruby', 'rails'] }

config_a[:tags] == config_b[:tags]  # => true
```

### 関連メソッドとの比較

#### `eql?` との違い

`eql?` はより厳密な等価性チェックを行います：

```ruby
[1, 2, 3] == [1.0, 2.0, 3.0]    # => true  (==)
[1, 2, 3].eql?([1.0, 2.0, 3.0]) # => false (eql?)
```

- `==`: 値が同じなら `true`（型変換を許容）
- `eql?`: 値も型も同じでないと `true` にならない

#### `<=>` との違い

`<=>` は大小比較を行い、-1、0、1 を返します：

```ruby
[1, 2, 3] <=> [1, 2, 3]  # => 0  (等しい)
[1, 2, 3] <=> [1, 2, 4]  # => -1 (小さい)
[1, 2, 5] <=> [1, 2, 4]  # => 1  (大きい)
```

### 注意点

1. **ネストした配列も再帰的に比較**
   ```ruby
   [[1, 2], [3, 4]] == [[1, 2], [3, 4]]  # => true
   [[1, 2], [3, 4]] == [[1, 2], [3, 5]]  # => false
   ```

2. **nil との比較**
   ```ruby
   [1, 2, 3] == nil  # => false
   ```

3. **空配列の比較**
   ```ruby
   [] == []  # => true
   ```

### パフォーマンスの考慮事項

- サイズチェックが最初に行われるため、サイズが異なる場合は高速
- サイズが同じ場合、全要素を順に比較するため、配列が大きいと時間がかかる
- 最悪の場合、O(n) の時間計算量（n は配列のサイズ）
  - つまり、配列が大きくなればなるほど、比較にかかる時間も増える
  - 配列が2倍になれば、処理時間もおおよそ2倍になる
  - 「最悪の場合」とは、全要素が一致しているケース（最後まで全部比較する必要がある）

### まとめ

`Array#==` は配列の内容を比較する際の基本的なメソッドです。テストやデータ検証でよく使われ、要素の型変換を許容する柔軟な比較が特徴です。より厳密な比較が必要な場合は `eql?` を検討しましょう。
