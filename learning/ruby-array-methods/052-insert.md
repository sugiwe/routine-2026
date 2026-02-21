# Array#insert

- 日付: 2026-02-21
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-insert
- daily 記録: [daily/2026-02-21.md](../../daily/2026-02-21.md)

## 基本情報

```ruby
insert(index, *objects) → self
```

指定されたオブジェクトを自身の要素として挿入し、自身を返します。

## ユーザーの学習記録（daily より）

### コード例

```ruby
# insert(index, *objects) → self
# Inserts the given objects as elements of self; returns self.
# 指定されたオブジェクトを自身の要素として挿入し、自身を返します。

# When index is non-negative, inserts objects before the element at offset index:
# インデックスが非負の場合、オフセットのインデックスにある要素の前にオブジェクトを挿入します。
a = ['a', 'b', 'c']     # => ["a", "b", "c"]
a.insert(1, :x, :y, :z) # => ["a", :x, :y, :z, "b", "c"]

# Extends the array if index is beyond the array (index >= self.size):
# インデックスが配列の範囲を超えている場合（index >= self.size）、配列を拡張します。
a = ['a', 'b', 'c']     # => ["a", "b", "c"]
a.insert(5, :x, :y, :z) # => ["a", "b", "c", nil, nil, :x, :y, :z]

# When index is negative, inserts objects after the element at offset index + self.size:
# インデックスが負の場合、オフセットが index + self.size の要素の後にオブジェクトを挿入します。
a = ['a', 'b', 'c']      # => ["a", "b", "c"]
a.insert(-2, :x, :y, :z) # => ["a", "b", :x, :y, :z, "c"]

# With no objects given, does nothing:
# オブジェクトが指定されていない場合は、何も行いません:
a = ['a', 'b', 'c'] # => ["a", "b", "c"]
a.insert(1)         # => ["a", "b", "c"]
a.insert(50)        # => ["a", "b", "c"]
a.insert(-50)       # => ["a", "b", "c"]

# Raises IndexError if objects are given and index is negative and out of range.
# オブジェクトが指定されていて、インデックスが負の値で範囲外の場合、IndexErrorが発生します。
a = ['a', 'b', 'c']
a.insert(-5, :x, :y, :z) # => 'Array#insert': index -5 too small for array; minimum: -4 (IndexError)

# Related: see Methods for Assigning.
```

## 深掘り・補足

### 重要なポイント

1. **破壊的メソッド**
   - 元の配列を直接変更する
   - `self` を返すのでメソッドチェーンが可能

2. **複数要素の一括挿入**
   - 可変長引数 `*objects` により、複数の要素を一度に挿入できる
   - 挿入される順序は引数の順序と同じ

3. **柔軟なインデックス指定**
   - 非負のインデックス: 指定位置の**前**に挿入
   - 負のインデックス: 末尾からの相対位置で挿入
   - 範囲外のインデックス: `nil` で埋められる（正の場合のみ）

### ユースケース

**リストの途中に要素を挿入**
```ruby
tasks = ['朝食', '仕事', '夕食']
tasks.insert(2, '昼食', '休憩')
# => ['朝食', '仕事', '昼食', '休憩', '夕食']
```

**末尾近くに挿入**
```ruby
menu = ['ファイル', '編集', 'ヘルプ']
menu.insert(-1, '表示', 'ツール')
# => ['ファイル', '編集', '表示', 'ツール', 'ヘルプ']
```

### 注意点

1. **負のインデックスのエラー範囲**
   - 配列サイズが 3 の場合、最小インデックスは `-4`
   - これ以下を指定するとオブジェクトがある場合のみ `IndexError`
   - オブジェクトがない場合は何もしない（エラーにならない）

2. **空の挿入は何もしない**
   - `insert(index)` は配列を変更しない
   - どんなインデックスを指定しても安全

3. **範囲外の正のインデックス**
   - 自動的に配列を拡張し、間を `nil` で埋める
   - 意図しない `nil` が混入する可能性があるので注意

### 関連メソッドとの比較

| メソッド | 挿入位置 | 複数要素 | 破壊的 |
|---------|---------|---------|--------|
| `insert` | 任意の位置 | ✓ | ✓ |
| `push` / `<<` | 末尾のみ | ✓ / 単一 | ✓ |
| `unshift` | 先頭のみ | ✓ | ✓ |
| `concat` | 末尾のみ | ✓ | ✓ |

**選び方のガイド**:
- 先頭・末尾への追加 → `push`, `unshift` の方がシンプル
- 途中の特定位置への挿入 → `insert` を使う
- 複数配列のマージ → `concat` が適している

### パフォーマンスの考慮事項

- 配列の先頭や途中への挿入は、それ以降の要素を全てシフトする必要があるため、O(n) の時間がかかる
- 頻繁に途中への挿入が必要な場合は、連結リストなど他のデータ構造を検討する価値がある
- 末尾への追加（`push`）の方が一般的に高速
