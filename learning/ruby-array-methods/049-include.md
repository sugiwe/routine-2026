# Array#include?

- 日付: 2026-02-18
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-include-3F
- daily 記録: [daily/2026-02-18.md](../../daily/2026-02-18.md)

## 基本情報

```ruby
include?(object) → true or false
```

`self` 内のいずれかの要素 `element` が `object == element` を満たす場合に `true` を返します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

（メモなし）

### コード例

```ruby
# Returns whether for some element `element` in `self`, `object == element`:
# `self`内のいずれかの要素`element`が`object`と等しい場合に`true`を返します。
[0, 1, 2].include?(2)   # => true
[0, 1, 2].include?(2.0) # => true
[0, 1, 2].include?(2.1) # => false
```

## 深掘り・補足

### 等値判定は `==` を使う

`include?` は `==` を使って比較します（`equal?` や `eql?` ではない）。

そのため `2` と `2.0` は `==` で等しいため `true` になります：

```ruby
2 == 2.0   # => true
[0, 1, 2].include?(2.0)  # => true（整数と浮動小数点数が等しいと判定される）
```

一方、`eql?` は型も一致させるため、`2.eql?(2.0)` は `false` になります。
`include?` は緩い比較をすることを意識しておくと良いでしょう。

### 公式ドキュメントの例

```ruby
browsers = ['Chrome', 'Firefox', 'Safari', 'Opera', 'IE']
browsers.include?('Konqueror')  # => false
browsers.include?('Chrome')     # => true
```

### ユースケース

```ruby
# 権限チェック
ADMIN_ROLES = ['admin', 'superuser']
ADMIN_ROLES.include?(current_user.role)  # => true / false

# 入力バリデーション
VALID_COLORS = ['red', 'green', 'blue']
VALID_COLORS.include?(params[:color])  # 不正な値の検出

# 条件分岐
fruits = ['apple', 'banana', 'cherry']
if fruits.include?('banana')
  puts "バナナあります"
end
```

### 注意点

- **線形探索（O(n)）**: 配列の先頭から順に比較するため、大きな配列では遅くなる
- 高速な検索が必要なら `Set#include?` または `Hash#key?` を検討する

```ruby
require 'set'
large_set = Set.new(large_array)
large_set.include?('target')  # O(1) でより高速
```

### 関連メソッドとの比較

| メソッド | 用途 | 等値判定 |
|---------|------|---------|
| `include?(obj)` | 要素の存在確認 | `==` |
| `any? { obj == _1 }` | include? と同等だが柔軟な条件が書ける | 任意 |
| `member?(obj)` | `include?` の別名 | `==` |
| `find_index(obj)` | 要素のインデックスも知りたい場合 | `==` |

```ruby
# member? は include? の別名
[1, 2, 3].member?(2)  # => true（include? と同じ）

# any? で柔軟な条件
[1, 2, 3].any? { |n| n > 2 }  # => true（include? では書けない範囲の条件も可）
```
