# Array#initialize_copy

- 日付: 2026-02-20
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-initialize_copy
- daily 記録: [daily/2026-02-20.md](../../daily/2026-02-20.md)

## 基本情報

```ruby
initialize_copy(other_array) → self
```

`Array#replace` のエイリアス。`self` の要素を `other_array` の要素で置き換えて `self` を返す。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

初めて聞くメソッド。`replace` のエイリアスらしい

### コード例

```ruby
# Replaces the elements of self with the elements of other_array, which must be an array-convertible object; returns self:
# 自身の要素を、配列に変換可能なオブジェクトであるother_arrayの要素で置き換えます。selfを返します。
a = ['a', 'b', 'c']             # => ["a", "b", "c"]
a.initialize_copy(['d', 'e'])   # => ["d", "e"]
a                               # => ["d", "e"]

# Related: see Methods for Assigning.
# Also aliased as: replace
```

## 深掘り・補足

### `replace` と `initialize_copy` の関係

通常のコードで使うのは `replace` のほう。`initialize_copy` は Ruby の内部的なフックメソッドで、`dup` や `clone` でオブジェクトをコピーするときに自動的に呼ばれます。

```ruby
class MyArray < Array
  def initialize_copy(orig)
    super
    # コピー時に追加の初期化処理を書ける
  end
end
```

Array を継承したクラスでコピー時の挙動をカスタマイズしたい場合にオーバーライドする用途が主です。一般ユーザーが直接呼ぶ機会はほぼありません。

### 破壊的メソッドである点に注意

`initialize_copy` / `replace` は **破壊的（in-place）操作** です。新しい配列を返すのではなく、レシーバー自身を変更します。

```ruby
a = [1, 2, 3]
b = a                         # b は a と同じオブジェクトを参照
a.initialize_copy([4, 5])
p a                           # => [4, 5]
p b                           # => [4, 5]  ← b も変わる（同じオブジェクトのため）
p a.equal?(b)                 # => true（オブジェクト ID は変わらない）
```

これは `a = [4, 5]` と異なります。`=` は変数を新しい配列に向け直すだけで、元のオブジェクトはそのままです。

### ユースケース

- **オブジェクト ID を維持したまま中身を差し替えたい**とき
- 他の変数が同じ配列を参照している状況で、すべての参照先に変更を反映させたいとき

### 注意点

- 引数が配列に変換できないオブジェクトの場合、`TypeError` が発生
- 凍結（`freeze`）された配列に対して呼ぶと `FrozenError` が発生

### 関連メソッド

| メソッド | 説明 |
|----------|------|
| `initialize_copy` / `replace` | 中身を置き換える（同じオブジェクト） |
| `a = other` | 変数を新しい配列に向け直す（別オブジェクト） |
| `concat` | 末尾に追加（置き換えではない） |
| `clear` | すべての要素を削除（`initialize_copy([])` と同じ） |
