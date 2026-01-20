# Array#collect!

- 日付: 2026-01-20
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-collect-21

## 基本情報

```ruby
collect! {|element| ... } → self
collect! → new_enumerator
```

`collect!` は配列の各要素にブロックを適用し、その結果で元の配列を上書きする破壊的メソッドです。`map!` のエイリアスとして機能します。

## daily からの記録

### メモ

```ruby
collect! {|element| ... } → self
collect! → new_enumerator
```

### コード例・補足

```ruby
# With a block given, calls the block with each element of self and replaces the element with the block's return value; returns self:
# ブロックが与えられた場合、selfの各要素に対してブロックを呼び出し、そのブロックの戻り値で要素を置き換えます。selfを返します。
a = [:foo, 'bar', 2]
a.map! { |element| element.class } # => [Symbol, String, Integer]

# With no block given, returns a new Enumerator.
# Related: collect; see also Methods for Converting.
# Also aliased as: map!
```

## 深掘り・補足

### 重要なポイント

1. **破壊的メソッド**
   - 元の配列を変更する（`!` が付いているメソッドは破壊的）
   - 新しい配列を作成せず、メモリ効率が良い
   - `self` を返すため、メソッドチェーンが可能

2. **`map!` のエイリアス**
   - `collect!` と `map!` は完全に同じ動作
   - Ruby コミュニティでは `map!` の方がよく使われる傾向

### ユースケース

#### 配列の各要素を変換する

```ruby
numbers = [1, 2, 3, 4, 5]
numbers.collect! { |n| n ** 2 }
# numbers は [1, 4, 9, 16, 25] に変更される
```

#### オブジェクトのプロパティを取得

```ruby
users = [user1, user2, user3]
users.collect! { |user| user.name }
# users は ["Alice", "Bob", "Charlie"] に変更される
```

#### 型変換

```ruby
strings = ["1", "2", "3"]
strings.collect! { |s| s.to_i }
# strings は [1, 2, 3] に変更される
```

### 注意点

1. **元の配列が失われる**
   ```ruby
   original = [1, 2, 3]
   original.collect! { |n| n * 2 }
   # original は [2, 4, 6] になり、元の値 [1, 2, 3] は失われる
   ```
   元の配列を保持したい場合は非破壊的な `collect` (または `map`) を使用する。

2. **ブロックなしの場合**
   ```ruby
   arr = [1, 2, 3]
   enumerator = arr.collect!
   # Enumerator が返される
   ```
   ブロックを渡さない場合は `Enumerator` が返される。

3. **凍結された配列には使えない**
   ```ruby
   arr = [1, 2, 3].freeze
   arr.collect! { |n| n * 2 }
   # FrozenError が発生
   ```

### パフォーマンスの考慮事項

- **メモリ効率**: 新しい配列を作成しないため、メモリ効率が良い
- **元の配列が不要な場合**: `collect!` を使う方が効率的
- **元の配列を保持する必要がある場合**: 非破壊的な `collect` を使う

### 関連メソッドとの比較

| メソッド | 破壊的 | 説明 |
|---------|--------|------|
| `collect` / `map` | ❌ | 新しい配列を返す |
| `collect!` / `map!` | ✅ | 元の配列を変更 |
| `each` | ❌ | 各要素を処理するが配列は変更しない |

### 実践的なアドバイス

- **命名規則を活用**: Ruby では `!` が付いているメソッドは破壊的という慣習がある
- **意図を明確に**: 元の配列を変更する意図が明確な場合にのみ使用
- **テストで確認**: 破壊的メソッドは予期しない副作用を生む可能性があるため、しっかりテストする
