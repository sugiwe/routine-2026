# Array#sample

- 日付: 2026-03-22
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-sample
- daily 記録: [daily/2026-03-22.md](../../daily/2026-03-22.md)

## 基本情報

### メソッドシグネチャ

```ruby
sample(n = nil, random: Random) → element or array
```

### 公式ドキュメントの概要

配列からランダムに要素を選択して返すメソッドです。引数なしの場合は単一の要素を、引数ありの場合は指定した個数の要素を配列として返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

`.sample(a.size)`は`shuffle`と同じことかな、おもろい

### コード例

```ruby
# Returns random elements from self, as selected by the object given by the keyword argument random.
# キーワード引数 random で指定されたオブジェクトによって選ばれた、自身の要素をランダムに返します。
# With no argument count given, returns one random element from self:
# 引数の数が指定されていない場合、selfからランダムに1つの要素を返します。
a = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
a.sample # => 3
a.sample # => 8

# Returns nil if self is empty:

[].sample # => nil

# With a non-negative numeric argument count given, returns a new array containing count random elements from self:
# 負ではない数値を引数countに指定すると、自身の要素からランダムにcount個選んだ新しい配列を返します。
a.sample(3) # => [8, 9, 2]
a.sample(6) # => [9, 6, 0, 3, 1, 4]

# The order of the result array is unrelated to the order of self.
# 結果配列の順序は、self の順序とは無関係です。
# Returns a new empty array if self is empty:

[].sample(4) # => []

# May return duplicates in self:
# 自身の中に重複が含まれる場合があります。
a = [1, 1, 1, 2, 2, 3]
a.sample(a.size) # => [1, 2, 1, 3, 1, 2]

# Returns no more than a.size elements (because no new duplicates are introduced):
# a.size以下の要素を返します（新しい重複が生成されることはないため）。
a.sample(50) # => [2, 1, 1, 3, 2, 1]

# The object given with the keyword argument random is used as the random number generator:
# キーワード引数「random」に指定されたオブジェクトが、乱数生成器として使用されます。
a = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
a.sample(random: Random.new(1))     # => 6
a.sample(4, random: Random.new(1))  # => [6, 10, 9, 2]

# Related: see Methods for Fetching.
```

## 深掘り・補足

### 重要なポイント

#### 1. 非破壊的メソッド

元の配列は変更されません。新しい要素または配列を返します。

```ruby
arr = [1, 2, 3, 4, 5]
arr.sample(3)  # => [2, 5, 1]
arr            # => [1, 2, 3, 4, 5] (変更なし)
```

#### 2. 重複なしで選択

**同じ要素が複数回選ばれることはありません**（元の配列に重複がある場合を除く）。これは `shuffle.first(n)` と同じ動作です。

```ruby
arr = [1, 2, 3]
arr.sample(2)  # => [3, 1] または [2, 1] など (重複なし)
```

#### 3. `sample(array.size)` と `shuffle` の関係

ユーザーの気づきは正確です！

```ruby
arr = [1, 2, 3, 4, 5]

# 以下の2つはほぼ同じ結果
arr.sample(arr.size)  # => [3, 1, 5, 2, 4]
arr.shuffle           # => [5, 2, 4, 1, 3]
```

どちらも全要素をランダムな順序で返しますが、内部実装は異なる場合があります。

### ユースケース

#### 1. ランダムな抽選

```ruby
participants = ["Alice", "Bob", "Charlie", "David"]
winner = participants.sample
puts "Winner: #{winner}"
```

#### 2. ランダムテストデータの生成

```ruby
test_cases = all_test_cases.sample(10)
test_cases.each { |tc| run_test(tc) }
```

#### 3. ゲームのランダムイベント

```ruby
random_enemies = enemy_list.sample(3)
spawn_enemies(random_enemies)
```

#### 4. ランダムな推薦システム

```ruby
featured_products = all_products.sample(5)
display_featured(featured_products)
```

### 注意点

#### 1. 空配列の場合

```ruby
[].sample      # => nil
[].sample(3)   # => []
```

引数なしの場合は `nil` が返るので注意が必要です。

#### 2. 要求数が配列サイズを超える場合

```ruby
arr = [1, 2, 3]
arr.sample(10)  # => [2, 3, 1] (最大で arr.size 個まで)
```

新しい重複は生成されないため、配列サイズ以上の要素は返りません。

#### 3. 乱数生成器のカスタマイズ

テスト時などに再現可能なランダムさが必要な場合：

```ruby
# 同じシードを使えば毎回同じ結果
arr.sample(3, random: Random.new(42))  # => [5, 1, 3]
arr.sample(3, random: Random.new(42))  # => [5, 1, 3] (同じ)
```

### パフォーマンスの考慮事項

- `sample(1)` は `sample` と比べて配列を返すため、わずかにオーバーヘッドがある
- 大きな配列で `sample(n)` を使う場合、`n` が小さいほど効率的
- `sample(array.size)` は実質的に `shuffle` と同じだが、意図を明確にするなら `shuffle` を使う方が可読性が高い

### 関連メソッドとの比較

| メソッド | 動作 | 戻り値 |
|---------|------|--------|
| `sample` | ランダムに1要素 | 要素または nil |
| `sample(n)` | ランダムにn要素（重複なし） | 配列 |
| `shuffle` | 全要素をランダムに並べ替え | 配列 |
| `first(n)` | 先頭からn要素 | 配列 |
| `last(n)` | 末尾からn要素 | 配列 |
