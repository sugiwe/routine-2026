# Array#shuffle

- 日付: 2026-03-26
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-shuffle
- daily 記録: [daily/2026-03-26.md](../../daily/2026-03-26.md)

## 基本情報

```ruby
shuffle → new_array
shuffle(random: rng) → new_array
```

キーワード引数 `random` で指定されたオブジェクトに基づいて、自身に含まれるすべての要素をランダムな順序で並べ替えた新しい配列を返します。元の配列は変更されません（非破壊的メソッド）。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# Returns a new array containing all elements from self in a random order, as selected by the object given by the keyword argument random:
# キーワード引数 random で指定されたオブジェクトに基づいて、自身に含まれるすべての要素をランダムな順序で並べ替えた新しい配列を返します。
a =            [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
a.shuffle # => [0, 8, 1, 9, 6, 3, 4, 7, 2, 5]
a.shuffle # => [8, 9, 0, 5, 1, 2, 6, 4, 7, 3]

# Duplicate elements are included:
# 重複する要素が含まれています。
a =            [0, 1, 0, 1, 0, 1, 0, 1, 0, 1]
a.shuffle # => [1, 0, 1, 1, 0, 0, 1, 0, 0, 1]
a.shuffle # => [1, 1, 0, 0, 0, 1, 1, 0, 0, 1]

# The object given with the keyword argument random is used as the random number generator.
# キーワード引数「random」に指定されたオブジェクトが、乱数生成器として使用されます。
# Related: see Methods for Fetching.
```

## 深掘り・補足

### 重要なポイント

1. **非破壊的メソッド**
   - 元の配列を変更せず、新しい配列を返す
   - 元の配列を保持したい場合に安全に使える
   - 破壊的版の `shuffle!` も存在する

2. **毎回異なる順序**
   - 呼び出すたびに異なるランダムな順序を生成
   - 同じ配列に対して何度呼んでも、異なる結果が得られる

3. **重複要素も保持**
   - 配列内に重複する要素があっても、すべて保持される
   - 要素の削除や追加は行われない

### 実践的なユースケース

#### 1. ゲーム開発

```ruby
# トランプのカードをシャッフル
cards = (1..52).to_a
shuffled_deck = cards.shuffle

# サイコロの目をランダムに並べ替え
dice_faces = [1, 2, 3, 4, 5, 6]
random_order = dice_faces.shuffle
```

#### 2. クイズアプリケーション

```ruby
# 質問の順序をランダム化
questions = [
  "質問1",
  "質問2",
  "質問3"
]
randomized_questions = questions.shuffle
```

#### 3. プレイリストのシャッフル

```ruby
# 音楽プレイリストをランダム再生
playlist = ["Song A", "Song B", "Song C", "Song D"]
shuffled_playlist = playlist.shuffle
```

#### 4. テストデータの生成

```ruby
# ユーザーをランダムな順序でテストに割り当て
users = User.all.to_a
random_test_order = users.shuffle
```

### 関連メソッドとの比較

| メソッド | 説明 | 破壊的 | 戻り値 |
|---------|------|--------|--------|
| `shuffle` | ランダムに並び替えた新しい配列を返す | ❌ | 新しい配列 |
| `shuffle!` | 配列自身をランダムに並び替える | ✅ | 変更後の配列 |
| `sample` | ランダムに1つまたは複数の要素を取得 | ❌ | 要素または配列 |
| `sort` | ソートした新しい配列を返す | ❌ | 新しい配列 |
| `reverse` | 逆順にした新しい配列を返す | ❌ | 新しい配列 |

### カスタム乱数生成器の使用

`random:` キーワード引数を使って、独自の乱数生成器を指定できます：

```ruby
# 再現可能なシャッフル（シード値を固定）
rng = Random.new(12345)
a = [1, 2, 3, 4, 5]
a.shuffle(random: rng)  # => 常に同じ順序になる

# 別のシード値で別の順序
rng2 = Random.new(67890)
a.shuffle(random: rng2)  # => 別の順序
```

これは、テストで再現可能なランダム性が必要な場合に便利です。

### パフォーマンスの考慮事項

- **時間計算量**: O(n) - Fisher-Yates シャッフルアルゴリズムを使用
- **空間計算量**: O(n) - 新しい配列を作成するため、元の配列と同じサイズのメモリが必要
- **大規模配列**: 数千〜数万要素でも効率的に動作

### 注意点

1. **元の配列を変更したくない場合**: `shuffle` を使う（デフォルトで安全）
2. **元の配列を変更したい場合**: `shuffle!` を使う（メモリ効率が良い）
3. **テストでの使用**: シード値を固定した乱数生成器を使うと、テストが再現可能になる

### 破壊的版 `shuffle!` との使い分け

```ruby
# 非破壊的（元の配列を保持）
original = [1, 2, 3, 4, 5]
shuffled = original.shuffle
original  # => [1, 2, 3, 4, 5] (変更なし)
shuffled  # => [3, 1, 5, 2, 4] (ランダム)

# 破壊的（元の配列を変更）
array = [1, 2, 3, 4, 5]
array.shuffle!
array  # => [3, 1, 5, 2, 4] (変更された)
```

**使い分けの基準**:
- 元の配列が後で必要 → `shuffle`
- 元の配列が不要、メモリを節約したい → `shuffle!`
