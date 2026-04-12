# Array#values_at

- 日付: 2026-04-10
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-values_at
- daily 記録: [daily/2026-04-10.md](../../daily/2026-04-10.md)

## 基本情報

```ruby
ary.values_at(*selectors) → new_array
```

`values_at` は、指定された識別子（インデックスまたは Range）に基づいて、配列から要素を選択し、新しい配列として返します。**自身の配列は変更されません**（非破壊的メソッド）。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

今回やたら解説が長いな👀 `values_at`という言葉の意味から連想される結果はなんとなくイメージがついた

### コード例

daily に詳細なコード例が記録されています（以下抜粋）：

```ruby
a = ['a', 'b', 'c', 'd']

# Index specifiers.
a.values_at(2, 0, 2, 0)     # => ["c", "a", "c", "a"] # May repeat.
a.values_at(-4, -3, -2, -1) # => ["a", "b", "c", "d"] # Counts backwards if negative.
a.values_at(-50, 50)        # => [nil, nil]           # Outside of self.

# Range specifiers.
a.values_at(1..3)       # => ["b", "c", "d"] # From range.begin to range.end.
a.values_at(1...3)      # => ["b", "c"]      # End excluded.
a.values_at(3..1)       # => []              # No such elements.
```

## 深掘り・補足

### 主な特徴

1. **非破壊的**: 元の配列は変更されず、新しい配列が返される
2. **複数の識別子を受け取る**: 可変長引数で複数のインデックスや Range を指定可能
3. **順序と重複を保持**: 指定した順序で要素を返し、同じインデックスを複数回指定できる
4. **範囲外は nil**: 存在しないインデックスを指定すると `nil` が返される

### インデックス指定の挙動

#### 正のインデックス

```ruby
a.values_at(0, 2)     # => ["a", "c"]
a.values_at(0.1, 2.9) # => ["a", "c"]  # 小数点は切り捨て
```

#### 負のインデックス

```ruby
a.values_at(-1, -4) # => ["d", "a"]  # 末尾から逆順にカウント
```

#### 範囲外アクセス

```ruby
a.values_at(4, -5) # => [nil, nil]  # 範囲外は nil
```

#### 重複と順序

```ruby
a.values_at(2, 0, 1, 0, 2) # => ["c", "a", "b", "a", "c"]  # 順序と重複を保持
```

### Range 指定の挙動

#### 基本的な Range

```ruby
a.values_at(1..2)  # => ["b", "c"]   # 終端を含む
a.values_at(1...2) # => ["b"]        # 終端を含まない
```

#### 負の range.begin

```ruby
a.values_at(-2..3) # => ["c", "d"]  # 開始位置が負の場合は末尾から
a.values_at(-5..3) # Raises RangeError  # 範囲外の負の開始位置はエラー
```

#### 範囲外の range.end

```ruby
a.values_at(1..5) # => ["b", "c", "d", nil, nil]  # nil で埋める
a.values_at(1..-2) # => ["b", "c"]  # 負の終端は末尾から
a.values_at(1..-5) # => []  # 負の終端が範囲外の場合は空配列
```

### 複数の識別子を混在させる

```ruby
a.values_at(3, 1..2, 0, 2..3) # => ["d", "b", "c", "a", "c", "d"]
```

インデックス指定と Range 指定を自由に組み合わせることができます。

### 実践的なユースケース

#### 特定のカラムを抽出

```ruby
row = ['Alice', 25, 'Engineer', 'Tokyo', 'alice@example.com']
name, job = row.values_at(0, 2)  # => ["Alice", "Engineer"]
```

#### 配列の並び替え

```ruby
data = ['a', 'b', 'c', 'd']
reordered = data.values_at(3, 1, 2, 0)  # => ["d", "b", "c", "a"]
```

#### 複数の範囲を一度に取得

```ruby
items = (1..10).to_a
values_at(0..2, 5..7, 9)  # => [1, 2, 3, 6, 7, 8, 10]
```

### 注意点

1. **RangeError の可能性**: 負の `range.begin` が範囲外の場合、`RangeError` が発生
2. **空配列のケース**: 引数なしの場合は空配列 `[]` を返す
3. **小数点の扱い**: 小数のインデックスは切り捨てられる

### 関連メソッドとの比較

- **`Array#[]` (slice)**: 1つのインデックスや Range を扱う。複数の識別子は指定できない
- **`Array#fetch`**: 範囲外アクセス時にデフォルト値やブロックを指定できる。複数の識別子は指定できない
- **`Array#slice`**: `[]` のエイリアス。同様の制約
- **`Array#select`**: 条件に基づいて要素を選択。インデックス指定はできない

`values_at` は**複数のインデックスや Range を一度に指定して、柔軟に要素を抽出したい場合**に最適です。

### パフォーマンスの考慮事項

- 識別子の数に比例して処理時間が増加
- 内部的には各インデックスや Range を順番に処理
- 大量のインデックス指定は避け、必要な要素のみを指定するのがベスト

---

**🎉 Ruby Array Methods 100番目の学習完了！**

`values_at` は非常に柔軟で強力なメソッドです。複数のインデックスや Range を自由に組み合わせられるため、配列から必要な要素を効率的に抽出できます。
