# Array#all?

- 日付: 2026-01-11
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-all-3F

## 基本情報

### メソッドシグネチャ

```ruby
all? → true or false
all?(object) → true or false
all? {|element| ... } → true or false
```

すべての要素がある条件を満たすかどうかを返すメソッドです。

## daily からの記録

### メモ

self の各要素が指定された条件を満たすかどうかを返します。

### コード例・補足

```ruby
all? → true or false
all?(object) → true or false
all? {|element| ... } → true or false
```

```ruby
# Returns whether for every element of self, a given criterion is satisfied.
# With no block and no argument, returns whether every element of self is truthy:
# self の各要素が指定された条件を満たすかどうかを返します。
# ブロックも引数も指定しない場合、self の各要素が真値であるかどうかを返します：
[[], {}, '', 0, 0.0, Object.new].all? # => true  # All truthy objects.
[[], {}, '', 0, 0.0, nil].all?        # => false # nil is not truthy.
[[], {}, '', 0, 0.0, false].all?      # => false # false is not truthy.

# With argument object given, returns whether object === ele for every element ele in self:
# 引数としてオブジェクトを渡すと、self内の各要素 ele について、object === ele であるかどうかを返します。
[0, 0, 0].all?(0)                    # => true
[0, 1, 2].all?(1)                    # => false
['food', 'fool', 'foot'].all?(/foo/) # => true
['food', 'drink'].all?(/foo/)        # => false

# With a block given, calls the block with each element in self; returns whether the block returns only truthy values:
# ブロックが与えられた場合、selfの各要素でそのブロックを呼び出します。ブロックが真値のみを返すかどうかを返します:
[0, 1, 2].all? { |ele| ele < 3 } # => true
[0, 1, 2].all? { |ele| ele < 2 } # => false

# With both a block and argument object given, ignores the block and uses object as above.
# ブロックと引数オブジェクトの両方が与えられた場合、ブロックを無視し、上記のようにオブジェクトを使用する。
# Special case: returns true if self is empty (regardless of any given argument or block).
# 特例：selfが空の場合（与えられた引数やブロックに関係なく）、trueを返す。
```

## 深掘り・補足

### 使い分けの 3 パターン

`all?` メソッドには 3 つの呼び出し方があり、それぞれ用途が異なります。

#### 1. ブロックも引数もなし：真値チェック

```ruby
[1, 2, 3].all?           # => true （すべて真値）
[1, 2, nil].all?         # => false（nil は真値ではない）
[1, 2, false].all?       # => false（false は真値ではない）
[].all?                  # => true （空配列は常に true）
```

**ポイント**：Ruby では `nil` と `false` のみが偽値で、それ以外（0 や空文字列 `""` も含む）はすべて真値です。

#### 2. パターンマッチング：`===` 演算子を使用

```ruby
# 型チェック
[1, 2, 3].all?(Integer)           # => true
[1, 2, 3.5].all?(Integer)         # => false

# 正規表現マッチ
['foo', 'food', 'fool'].all?(/fo/) # => true
['foo', 'bar'].all?(/fo/)          # => false

# 範囲チェック
[1, 2, 3].all?(1..5)              # => true
[1, 2, 10].all?(1..5)             # => false

# 値の一致
[5, 5, 5].all?(5)                 # => true
[5, 5, 6].all?(5)                 # => false
```

**ポイント**：`===` 演算子（case equality operator）を使うので、クラス、正規表現、範囲などさまざまなパターンマッチングが可能です。

#### 3. ブロック指定：カスタム条件

```ruby
# 数値の条件チェック
[1, 2, 3].all? { |n| n > 0 }      # => true
[1, 2, 3].all? { |n| n.even? }    # => false

# 文字列の長さチェック
['a', 'ab', 'abc'].all? { |s| s.length <= 3 } # => true

# オブジェクトの属性チェック
users.all? { |user| user.active? }
products.all? { |product| product.price > 0 }
```

### 重要な特性

#### 短絡評価（Short-circuit evaluation）

最初に条件を満たさない要素が見つかった時点で、それ以降の評価は行われません：

```ruby
# 2番目の要素で false になるため、3番目以降は評価されない
[1, 2, 3, 4, 5].all? { |n| puts "checking #{n}"; n < 3 }
# 出力:
# checking 1
# checking 2
# checking 3
# => false
```

#### 空配列の扱い

論理学における「全称量化」の定義に従い、空配列に対しては常に `true` を返します：

```ruby
[].all?                          # => true
[].all?(Integer)                 # => true
[].all? { |n| n > 1000 }        # => true
```

これは「すべての要素が条件を満たす」が「条件を満たさない要素が存在しない」と解釈されるためです。

### 実践的なユースケース

#### バリデーション

```ruby
# すべてのフォーム入力が有効かチェック
form_fields.all? { |field| field.valid? }

# すべての必須項目が入力されているかチェック
required_fields.all? { |field| field.present? }
```

#### データの整合性チェック

```ruby
# すべての価格が正の数かチェック
prices.all? { |price| price > 0 }

# すべての ID がユニークかチェック（配列の長さと uniq 後の長さを比較）
ids.length == ids.uniq.length
```

#### 権限チェック

```ruby
# すべてのユーザーが管理者権限を持っているかチェック
users.all? { |user| user.admin? }

# すべてのファイルが読み取り可能かチェック
files.all? { |file| File.readable?(file) }
```

### 注意点

#### ブロックと引数の両方を指定した場合

```ruby
# ブロックは無視され、引数のパターンマッチングのみが使われる
[1, 2, 3].all?(Integer) { |n| n > 10 }  # => true （ブロックは無視される）
```

この動作は混乱を招くので、**両方を同時に指定することは避けるべき**です。

#### 副作用のあるブロックは避ける

短絡評価により、すべての要素が評価されるとは限りません：

```ruby
# 悪い例：副作用に依存
counter = 0
[1, 2, 3].all? { |n| counter += 1; n > 0 }
# counter は 3 になるとは限らない
```

### 関連メソッドとの比較

| メソッド | 説明 | 空配列の結果 |
|---------|------|------------|
| `all?` | すべての要素が条件を満たす | `true` |
| `any?` | 少なくとも1つの要素が条件を満たす | `false` |
| `none?` | どの要素も条件を満たさない | `true` |
| `one?` | 正確に1つの要素だけが条件を満たす | `false` |

```ruby
arr = [1, 2, 3, 4, 5]

arr.all? { |n| n > 0 }   # => true （すべて正の数）
arr.any? { |n| n > 3 }   # => true （3より大きい数がある）
arr.none? { |n| n > 10 } # => true （10より大きい数はない）
arr.one? { |n| n == 3 }  # => true （3は1つだけ）
```

### パフォーマンスの考慮事項

- **短絡評価**により、条件を満たさない要素が早期に見つかれば、残りの要素は評価されません
- 大きな配列で複雑な条件を評価する場合、条件を満たさない可能性が高い要素を先頭に配置すると効率的
- ブロック内の処理はできるだけシンプルに保つ

`all?` メソッドは、配列の全要素に対する条件チェックを簡潔に記述できる強力なメソッドです。適切に使うことで、コードの可読性と保守性を大きく向上させることができます。
