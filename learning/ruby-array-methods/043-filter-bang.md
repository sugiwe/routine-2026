# Array#filter!

- 日付: 2026-02-12
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-filter-21
- daily 記録: [daily/2026-02-12.md](../../daily/2026-02-12.md)

## 基本情報

```ruby
filter! {|element| ... } → self or nil
filter! → new_enumerator
```

`filter!` は `select!` のエイリアスです。ブロックが与えられた場合、自身の各要素に対してブロックを呼び出し、ブロックが `false` または `nil` を返した要素を自身から削除します。

**破壊的メソッド**であり、元の配列を変更します。

### 戻り値

- **要素が削除された場合**: `self`（変更後の配列）を返す
- **要素が削除されなかった場合**: `nil` を返す
- **ブロックが与えられない場合**: `Enumerator` を返す

## ユーザーの学習記録（daily より）

### 第一印象のメモ

`select!`のエイリアス

### コード例

```ruby
# filter! {|element| ... } → self or nil
# filter! → new_enumerator

# With a block given, calls the block with each element of self; removes from self those elements for which the block returns false or nil.
# ブロックが与えられた場合、自身の各要素に対してブロックを呼び出し、ブロックがfalseまたはnilを返した要素を自身から削除します。
# Returns self if any elements were removed:
# 要素が削除された場合、自身を返します。
a = [:foo, 'bar', 2, :bam]
a.select! {|element| element.to_s.start_with?('b') } # => ["bar", :bam]
a # => ["bar", :bam]

# Returns nil if no elements were removed.
# 要素が削除されなかった場合、nilを返します。
# With no block given, returns a new Enumerator.
# ブロックが与えられない場合、新しいEnumeratorを返します。
```

## 深掘り・補足

### `filter!` と `select!` の関係

`filter!` は `select!` の**完全なエイリアス**です。機能的な違いは一切ありません。

```ruby
# 以下は完全に同じ動作
array.filter! { |x| x > 5 }
array.select! { |x| x > 5 }
```

### なぜエイリアスが存在するのか

`filter` / `select` という名前の違いは、プログラミングコミュニティでの慣習の違いに由来します：

- **`select`**: Ruby の伝統的な命名（Smalltalk 由来）
- **`filter`**: 他の言語（JavaScript、Python など）でよく使われる名前

Ruby は両方の命名スタイルをサポートすることで、他言語からの移行者にも親しみやすくしています。

### 破壊的メソッドとしての特徴

#### 重要な戻り値の挙動

`filter!` の戻り値は、**要素が削除されたかどうか**によって変わります：

```ruby
# 要素が削除された場合 → self を返す
a = [1, 2, 3, 4]
result = a.filter! { |x| x > 2 }
result # => [3, 4]
a      # => [3, 4]
result.object_id == a.object_id # => true

# 要素が削除されなかった場合 → nil を返す
b = [5, 6, 7]
result = b.filter! { |x| x > 2 }
result # => nil
b      # => [5, 6, 7] (変更なし)
```

この挙動は、変更があったかどうかを判定するのに便利です：

```ruby
if array.filter! { |x| x.valid? }
  puts "無効な要素を削除しました"
else
  puts "すべての要素が有効です"
end
```

### 非破壊的メソッドとの比較

| メソッド | 破壊的 | 戻り値 | 用途 |
|---------|-------|-------|------|
| `filter` / `select` | ❌ | 新しい配列 | 元の配列を保持したい場合 |
| `filter!` / `select!` | ✅ | self or nil | 元の配列を変更したい場合 |

```ruby
# 非破壊的
original = [1, 2, 3, 4, 5]
filtered = original.filter { |x| x > 3 }
original # => [1, 2, 3, 4, 5] (変更なし)
filtered # => [4, 5]

# 破壊的
original = [1, 2, 3, 4, 5]
original.filter! { |x| x > 3 }
original # => [4, 5] (変更された)
```

### 関連メソッドとの違い

#### `reject!` との関係

`reject!` は `filter!` の逆の動作をします：

```ruby
a = [1, 2, 3, 4, 5]
a.filter! { |x| x > 3 }  # 条件を満たす要素を残す
# => [4, 5]

b = [1, 2, 3, 4, 5]
b.reject! { |x| x > 3 }  # 条件を満たす要素を削除
# => [1, 2, 3]
```

#### `keep_if` との違い

`keep_if` も同様の動作をしますが、戻り値が異なります：

```ruby
# filter! → 変更がなければ nil を返す
a = [1, 2, 3]
a.filter! { |x| x > 0 } # => nil (すべて残る)

# keep_if → 常に self を返す
b = [1, 2, 3]
b.keep_if { |x| x > 0 } # => [1, 2, 3] (self)
```

### ユースケース

#### 1. 配列のフィルタリング（インプレース）

```ruby
# 無効なデータを削除
users.filter! { |user| user.active? }

# 重複を除去しながら条件でフィルタ
items.uniq!
items.filter! { |item| item.price > 100 }
```

#### 2. 条件付きクリーンアップ

```ruby
# 空白や nil を削除
values.filter! { |v| v && !v.strip.empty? }

# 不正な値を除外
numbers.filter! { |n| n.is_a?(Integer) && n > 0 }
```

#### 3. メソッドチェーン

```ruby
# 破壊的メソッドのチェーン（注意が必要）
data.filter! { |x| x.valid? }
    &.map! { |x| x.process }
    &.compact!
```

### 注意点とベストプラクティス

#### 1. 戻り値が nil の可能性を考慮

```ruby
# ❌ 危険：nil が返る可能性がある
result = array.filter! { |x| x > 5 }.map { |x| x * 2 }
# array に変更がなければ nil.map でエラー

# ✅ 安全：nil チェック
array.filter! { |x| x > 5 }
result = array.map { |x| x * 2 }

# ✅ または、非破壊的メソッドを使う
result = array.filter { |x| x > 5 }.map { |x| x * 2 }
```

#### 2. 元の配列が必要な場合は非破壊的メソッドを使う

```ruby
# ❌ 破壊的メソッドで元の配列が失われる
original = [1, 2, 3, 4, 5]
original.filter! { |x| x > 3 }
# original の元の値は失われる

# ✅ 非破壊的メソッドで元の配列を保持
original = [1, 2, 3, 4, 5]
filtered = original.filter { |x| x > 3 }
# original と filtered の両方が使える
```

#### 3. イテレーション中の変更に注意

```ruby
# ⚠️ 他のイテレーターで同じ配列を操作中に filter! を使うと予期しない動作
array = [1, 2, 3, 4, 5]
array.each do |x|
  array.filter! { |y| y != x }  # 危険！
end
```

### パフォーマンスの考慮事項

#### メモリ効率

破壊的メソッド（`filter!`）は、新しい配列を作成しないため、メモリ効率が良いです：

```ruby
# 非破壊的 → 新しい配列を作成
large_array = (1..1_000_000).to_a
filtered = large_array.filter { |x| x.even? }
# メモリ：元の配列 + 新しい配列

# 破壊的 → 既存の配列を変更
large_array = (1..1_000_000).to_a
large_array.filter! { |x| x.even? }
# メモリ：元の配列のみ（サイズは縮小）
```

#### 実行速度

大きな配列では、破壊的メソッドの方が若干高速です（配列のコピーが不要なため）。

### まとめ

- `filter!` は `select!` のエイリアス（機能は完全に同じ）
- **破壊的メソッド**であり、元の配列を変更する
- 変更があれば `self`、なければ `nil` を返す
- メモリ効率が良いが、元の配列が失われる点に注意
- 戻り値が `nil` の可能性を考慮してコードを書く
