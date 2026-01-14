# Array#assoc

- 日付: 2026-01-14
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-assoc

## 基本情報

```ruby
assoc(object) → found_array or nil
```

配列内の要素を走査し、最初の要素が指定オブジェクトと等しい配列要素を返します。該当する要素がない場合は `nil` を返します。

## daily からの記録

### メモ

`assoc(object) → found_array or nil`

### 感想

初めて聞いたメソッドかも、少なくとも自分で使ったことはない

### コード例・補足

```ruby
# Returns the first element ele in self such that ele is an array and ele[0] == object:
# selfの中にある配列のうち、最初の要素eleで、その配列の0番目の要素がobjectと一致するものを返します。
a = [{foo: 0}, [2, 4], [4, 5, 6], [4, 5]]
a.assoc(4) # => [4, 5, 6]

# Returns nil if no such element is found.
# 該当する要素が見つからない場合はnilを返します。
```

## 深掘り・補足

### メソッドの特徴

`Array#assoc` は、**配列の配列（二次元配列）から特定のキーで要素を検索する**ための便利なメソッドです。連想配列（Association List）のような構造を扱う際に有用です。

### 重要なポイント

1. **ネストされた配列が前提**
   - 配列の要素自体が配列であることを想定
   - ハッシュなどの配列でない要素は無視される

2. **最初の要素で比較**
   - 各配列要素の `[0]` の位置の値で比較
   - `==` を使った等価性チェック

3. **最初にマッチしたもののみ返す**
   - 複数マッチする要素があっても、最初の1つだけ
   - 全てを取得したい場合は `select` などを使う

### 実践的な使用例

```ruby
# 設定のリストから特定の設定を取得
config = [
  [:host, 'localhost'],
  [:port, 3000],
  [:debug, true]
]

config.assoc(:port)  #=> [:port, 3000]
config.assoc(:ssl)   #=> nil

# 値だけ取得したい場合
port_value = config.assoc(:port)&.last  #=> 3000

# 複数の値を持つ設定
routes = [
  [:get, '/users', 'UsersController#index'],
  [:post, '/users', 'UsersController#create'],
  [:get, '/posts', 'PostsController#index']
]

routes.assoc(:get)  #=> [:get, '/users', 'UsersController#index']
# 注意: 最初の :get のみ返される
```

### ユースケース

1. **シンプルな連想配列**
   - Hash を使うほどでもない軽量なキー・バリューペア
   - 順序が重要な場合（Ruby 1.9以前との互換性など）

2. **設定ファイルのパース結果**
   - INI ファイルや設定ファイルの簡易的な表現

3. **レガシーコードとの互換性**
   - 古い Ruby コードで連想配列として使われていた構造

### 注意点

1. **パフォーマンス**
   - 線形探索（O(n)）なので、大量のデータには不向き
   - 頻繁な検索が必要なら Hash を使うべき

2. **型チェック**
   - 配列でない要素は単にスキップされる
   - エラーにならないので、データ構造の不整合に気づきにくい

```ruby
mixed = [[:a, 1], 'string', [:b, 2], {c: 3}]
mixed.assoc(:b)  #=> [:b, 2]
# 'string' と {c: 3} は無視される
```

3. **nil の扱い**
   - 見つからない場合は `nil` を返す
   - 要素が `[nil, value]` の場合と区別が必要

```ruby
arr = [[nil, 'value'], [:key, 'data']]
arr.assoc(nil)   #=> [nil, 'value']
arr.assoc(:foo)  #=> nil（見つからない）
```

### 関連メソッド

- **`rassoc(obj)`**: 値（2番目の要素）で検索する逆バージョン
- **`Hash[]`**: 配列の配列から Hash を作成
- **`to_h`**: 配列の配列を Hash に変換（Ruby 2.0+）

```ruby
config = [[:host, 'localhost'], [:port, 3000]]

# Hash に変換してから使う方が効率的
config_hash = config.to_h
config_hash[:port]  #=> 3000

# rassoc の例
config.rassoc('localhost')  #=> [:host, 'localhost']
```

### 現代的な代替手段

実務では Hash を使う方が一般的ですが、以下のような場面では `assoc` も有用です：

```ruby
# CSVデータのようなシンプルな構造
csv_data = [
  ['Name', 'Alice'],
  ['Age', '30'],
  ['City', 'Tokyo']
]

csv_data.assoc('Name')  #=> ['Name', 'Alice']
```
