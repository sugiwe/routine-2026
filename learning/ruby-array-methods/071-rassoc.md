# Array#rassoc

- 日付: 2026-03-12
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-rassoc
- daily 記録: [daily/2026-03-12.md](../../daily/2026-03-12.md)

## 基本情報

```ruby
rassoc(object) → found_array or nil
```

`self` に含まれる配列要素 `ele` のうち、`ele[1]` が `object` と等しい最初の要素を返します。該当する要素が見つからない場合は `nil` を返します。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# Returns the first element `ele` in `self` such that `ele` is an array and `ele[1] == object`:
# `self`に含まれる配列要素`ele`のうち、`ele[1]`が`object`と等しい最初の要素を返します。
a = [{foo: 0}, [2, 4], [4, 5, 6], [4, 5]]
a.rassoc(4) # => [2, 4]
a.rassoc(5) # => [4, 5, 6]

# Returns nil if no such element is found.
# Related: Array#assoc; see also Methods for Fetching.
```

## 深掘り・補足

### メソッドの役割

`rassoc` は「reverse association（逆連想配列）」の略で、**値（バリュー）からキー・バリューペアを検索する**ためのメソッドです。

### Array#assoc との比較

| メソッド | 検索対象 | 使用例 |
|---------|---------|--------|
| `assoc` | 配列の**最初の要素**（`ele[0]`）でマッチング | キーから値を探す |
| `rassoc` | 配列の**2番目の要素**（`ele[1]`）でマッチング | 値からキーを探す |

```ruby
a = [[:key1, "value1"], [:key2, "value2"], [:key3, "value1"]]

# assoc: キーで検索
a.assoc(:key2) # => [:key2, "value2"]

# rassoc: 値で検索（最初にマッチしたものを返す）
a.rassoc("value1") # => [:key1, "value1"]
```

### 重要なポイント

**1. 配列要素のみが対象**
```ruby
a = [{foo: 0}, [2, 4], [4, 5, 6], [4, 5]]
a.rassoc(4) # => [2, 4]
# ハッシュ {foo: 0} は無視される
```

**2. 最初にマッチした要素のみを返す**
```ruby
a = [[1, 100], [2, 100], [3, 100]]
a.rassoc(100) # => [1, 100] （最初の要素のみ）
```

**3. 等価性の判定は `==` を使用**
```ruby
a = [["a", 1], ["b", 1.0]]
a.rassoc(1)   # => ["a", 1]
a.rassoc(1.0) # => ["a", 1] （1 == 1.0 なので）
```

### ユースケース

**1. 逆引き辞書**
```ruby
# 都道府県コードから県名を探すのではなく、県名からコードを探す
prefectures = [[1, "北海道"], [2, "青森県"], [3, "岩手県"]]
prefectures.rassoc("青森県") # => [2, "青森県"]
```

**2. 設定値からキーを特定**
```ruby
settings = [[:theme, "dark"], [:lang, "ja"], [:timezone, "JST"]]
settings.rassoc("ja") # => [:lang, "ja"]
# 「ja」という値を持つ設定項目を見つける
```

**3. マッピングの逆引き**
```ruby
status_map = [[:pending, 0], [:active, 1], [:completed, 2]]
status_map.rassoc(1) # => [:active, 1]
# ステータスコード 1 がどのステータス名に対応するか調べる
```

### 注意点

**複数マッチする場合の動作**
```ruby
a = [[1, "x"], [2, "x"], [3, "x"]]
a.rassoc("x") # => [1, "x"] （最初のみ）
# すべて取得したい場合は select を使う
a.select { |ele| ele.is_a?(Array) && ele[1] == "x" }
# => [[1, "x"], [2, "x"], [3, "x"]]
```

**nil との比較**
```ruby
a = [[1, nil], [2, false]]
a.rassoc(nil)   # => [1, nil]
a.rassoc(false) # => [2, false]
# nil と false は区別される
```

### 関連メソッド

- `Array#assoc`: 最初の要素でマッチング
- `Array#find`: より柔軟な検索条件
- `Array#select`: 複数マッチする要素をすべて取得
- `Hash#key`: ハッシュで値からキーを検索（類似の用途）

### パフォーマンスの考慮

- 線形探索（O(n)）なので、大量のデータには向かない
- 頻繁に逆引きする場合は、`Hash` に変換するか、逆マッピング用の `Hash` を別途用意する方が効率的

```ruby
# 頻繁に逆引きする場合の最適化
pairs = [[1, "a"], [2, "b"], [3, "c"]]
reverse_map = pairs.to_h.invert # => {"a"=>1, "b"=>2, "c"=>3}
reverse_map["b"] # => 2 （O(1)で取得可能）
```
