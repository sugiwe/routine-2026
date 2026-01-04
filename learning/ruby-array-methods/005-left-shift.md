# Array#<<

- 日付: 2026-01-05
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-3C-3C

## 基本情報

```ruby
self << object → self
```

オブジェクトを配列の末尾に追加し、`self`（配列自身）を返します。

```ruby
# Appends object as the last element in self; returns self:
# オブジェクトをselfの末尾に追加する。selfを返す。
[:foo, 'bar', 2] << :baz # => [:foo, "bar", 2, :baz]

# Appends object as a single element, even if it is another array:
# オブジェクトを単一の要素として追加します。それが別の配列であっても同様です：
[:foo, 'bar', 2] << [3, 4] # => [:foo, "bar", 2, [3, 4]]
```

## メモ（daily より）

`self << object → self`

## 深掘り・補足

### 重要なポイント

1. **破壊的メソッド**: 元の配列を直接変更します
2. **戻り値は self**: 配列自身を返すため、メソッドチェーンが可能
3. **単一要素として追加**: 配列を渡しても、その配列全体が1つの要素になる

### メソッドチェーンの活用

`self` を返すため、連続して要素を追加できます：

```ruby
arr = [1, 2, 3]
arr << 4 << 5 << 6
# => [1, 2, 3, 4, 5, 6]

# 複数行で書くことも可能
result = []
  << "first"
  << "second"
  << "third"
# => ["first", "second", "third"]
```

### 配列を要素として追加する動作

```ruby
arr = [1, 2]
arr << [3, 4]
# => [1, 2, [3, 4]]  # ネストされた配列になる

# 配列を展開して追加したい場合は concat や + を使う
arr = [1, 2]
arr.concat([3, 4])
# => [1, 2, 3, 4]
```

### ユースケース

1. **ログやイベントの蓄積**
   ```ruby
   logs = []
   logs << { time: Time.now, message: "Started" }
   logs << { time: Time.now, message: "Processing" }
   ```

2. **条件付きで要素を追加**
   ```ruby
   results = []
   results << item if item.valid?
   ```

3. **ビルダーパターン**
   ```ruby
   query = []
   query << "SELECT *"
   query << "FROM users"
   query << "WHERE active = true" if filter_active
   sql = query.join(" ")
   ```

### 注意点

1. **破壊的操作**: 元の配列が変更されるため、不変性が必要な場合は注意
   ```ruby
   original = [1, 2, 3]
   modified = original << 4  # original も変更される
   # original => [1, 2, 3, 4]
   # modified => [1, 2, 3, 4]  # 同じオブジェクト
   ```

2. **freeze された配列には使えない**
   ```ruby
   arr = [1, 2, 3].freeze
   arr << 4  # FrozenError が発生
   ```

### 関連メソッドとの比較

| メソッド | 動作 | 複数要素 | 配列の展開 |
|---------|------|----------|-----------|
| `<<` | 末尾に追加 | × (1つずつ) | × (単一要素化) |
| `push` | 末尾に追加 | ○ | × (単一要素化) |
| `concat` | 末尾に結合 | ○ | ○ |
| `append` | `push` のエイリアス | ○ | × |
| `+` | 新配列を生成 | ○ | ○ |

```ruby
# push: 複数要素を一度に追加可能
arr = [1, 2]
arr.push(3, 4, 5)  # => [1, 2, 3, 4, 5]

# << を使う場合は連続呼び出し
arr = [1, 2]
arr << 3 << 4 << 5  # => [1, 2, 3, 4, 5]

# concat: 配列を展開して追加
arr = [1, 2]
arr.concat([3, 4])  # => [1, 2, 3, 4]

# <<: 配列を単一要素として追加
arr = [1, 2]
arr << [3, 4]  # => [1, 2, [3, 4]]
```

### パフォーマンスの考慮事項

- `<<` は配列の末尾に追加するため、O(1) の時間計算量（償却）
- 大量の要素を追加する場合、`concat` の方が効率的な場合がある
- メソッドチェーンは可読性は高いが、状況に応じて `push` との使い分けを検討

### まとめ

`<<` は Ruby で最もよく使われる配列操作の1つで、シンプルで直感的な構文が特徴です。`self` を返すメソッドチェーンの特性を活かして、簡潔なコードを書くことができます。ただし、破壊的メソッドであることと、配列を単一要素として追加する動作には注意が必要です。
