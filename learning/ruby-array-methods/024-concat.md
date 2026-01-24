# Array#concat

- 日付: 2026-01-24
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-concat

## 基本情報

```ruby
concat(*other_arrays) → self
```

`concat` は、引数として渡された各配列のすべての要素を self（呼び出し元の配列）に追加し、self を返す破壊的メソッドです。

## daily からの記録

### メモ

`concat(*other_arrays) → self`

感想: 引数の中に配列があった場合は配列が展開されて渡されるってこと？

### コード例・補足

```ruby
# Adds to self all elements from each array in other_arrays; returns self:
# other_arrays 内の各配列の要素をすべて self に追加します。self を返します。
a = [0, 1]
a.concat(['two', 'three'], [:four, :five], a)
# => [0, 1, "two", "three", :four, :five, 0, 1]
```

## 深掘り・補足

### 重要なポイント

#### 1. 破壊的メソッド

`concat` は元の配列を**直接変更**する破壊的メソッドです。新しい配列を返すのではなく、self を変更して返します。

```ruby
a = [1, 2]
result = a.concat([3, 4])
p a      # => [1, 2, 3, 4]（元の配列が変更されている）
p result # => [1, 2, 3, 4]（同一オブジェクト）
p a.object_id == result.object_id # => true
```

#### 2. 複数の配列を一度に追加可能

可変長引数 `*other_arrays` により、複数の配列を一度に結合できます：

```ruby
a = [1, 2]
a.concat([3, 4], [5, 6], [7, 8])
# => [1, 2, 3, 4, 5, 6, 7, 8]
```

#### 3. 配列の要素が展開される

引数として渡された配列は**展開**されて追加されます。配列自体がネストされるわけではありません：

```ruby
a = [1, 2]
a.concat([3, [4, 5]])
# => [1, 2, 3, [4, 5]]  # 外側の配列は展開されるが、内側の [4, 5] はそのまま
```

#### 4. 自分自身を追加することも可能

daily のコード例にあるように、配列自身を引数に渡すこともできます：

```ruby
a = [0, 1]
a.concat(a)
# => [0, 1, 0, 1]  # 現在の要素のコピーが追加される
```

### ユースケース

#### 複数のデータソースを統合

```ruby
morning_tasks = ['ラジオ体操', '洗顔']
evening_tasks = ['スクワット', '腹筋']
night_tasks = ['学習', '日記']

all_tasks = []
all_tasks.concat(morning_tasks, evening_tasks, night_tasks)
# => ["ラジオ体操", "洗顔", "スクワット", "腹筋", "学習", "日記"]
```

#### ループ内での配列結合

```ruby
results = []
files.each do |file|
  data = process_file(file)  # 配列を返すと仮定
  results.concat(data)        # 各ファイルの結果を統合
end
```

### 関連メソッドとの比較

| メソッド | 動作 | 破壊的 | 例 |
|---------|------|--------|-----|
| `concat` | 配列の要素を展開して追加 | ✓ | `[1].concat([2, 3])` → `[1, 2, 3]` |
| `push` / `append` | 引数をそのまま末尾に追加 | ✓ | `[1].push([2, 3])` → `[1, [2, 3]]` |
| `<<` | 1つの要素を末尾に追加 | ✓ | `[1] << [2, 3]` → `[1, [2, 3]]` |
| `+` | 配列を結合して新しい配列を返す | ✗ | `[1] + [2, 3]` → `[1, 2, 3]` |
| `|` | 和集合（重複削除） | ✗ | `[1, 2] | [2, 3]` → `[1, 2, 3]` |

#### concat vs push の違い

```ruby
# concat: 配列を展開して追加
a = [1, 2]
a.concat([3, 4])
# => [1, 2, 3, 4]

# push: 配列をそのまま追加（ネスト）
b = [1, 2]
b.push([3, 4])
# => [1, 2, [3, 4]]
```

#### concat vs + の違い

```ruby
# concat: 破壊的（元の配列を変更）
a = [1, 2]
a.concat([3, 4])
p a  # => [1, 2, 3, 4]

# +: 非破壊的（新しい配列を生成）
b = [1, 2]
c = b + [3, 4]
p b  # => [1, 2]（元の配列は変更されない）
p c  # => [1, 2, 3, 4]
```

### 注意点

#### 1. 破壊的操作による副作用

元の配列が変更されるため、他の場所で同じ配列を参照している場合は影響を受けます：

```ruby
original = [1, 2]
reference = original  # 同じオブジェクトを参照

original.concat([3, 4])
p reference  # => [1, 2, 3, 4]（影響を受ける）
```

元の配列を保持したい場合は、先に `dup` や `clone` で複製を作成します：

```ruby
original = [1, 2]
copy = original.dup
copy.concat([3, 4])
p original  # => [1, 2]（変更されない）
p copy      # => [1, 2, 3, 4]
```

#### 2. 引数が配列でない場合

引数が配列でない場合、TypeError が発生します：

```ruby
a = [1, 2]
a.concat(3)  # TypeError: no implicit conversion of Integer into Array
```

配列に変換するか、`push` を使用する必要があります：

```ruby
a = [1, 2]
a.concat([3])  # => [1, 2, 3]
# または
a.push(3)      # => [1, 2, 3]
```

### パフォーマンスの考慮

`concat` は内部的に効率的な実装がされているため、ループ内で `<<` や `push` を繰り返し使用するよりも高速です：

```ruby
# 非効率
result = []
data.each { |item| result << item }

# 効率的
result = []
result.concat(data)
```

### まとめ

- `concat` は複数の配列を一度に結合できる破壊的メソッド
- 配列の要素が展開されて追加される（ネストしない）
- 元の配列を変更するため、副作用に注意
- 非破壊的に結合したい場合は `+` を使用
- 配列自体を追加したい場合は `push` を使用
