# Array#freeze

- 日付: 2026-02-17
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-freeze
- daily 記録: [daily/2026-02-17.md](../../daily/2026-02-17.md)

## 基本情報

```ruby
freeze → self
```

配列を凍結し（まだ凍結されていない場合）、自身を返します。凍結された配列は、それ以降変更することができません。変更を試みると `FrozenError` が発生します。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

（メモなし）

### コード例

```ruby
# Freezes self (if not already frozen); returns self:
# 自身を凍結し（すでに凍結されている場合を除く）、自身を返します。

a = []
a.frozen? # => false
a.freeze
a.frozen? # => true

# No further changes may be made to self; raises FrozenError if a change is attempted.
# それ以降、自身を変更することはできません。変更を試みるとFrozenError が発生します。
```

## 深掘り・補足

### `freeze` メソッドとは

`freeze` は Ruby のオブジェクトを不変（immutable）にするためのメソッドで、`Array` を含むすべてのオブジェクトに対して使用できます。一度凍結されたオブジェクトは、その状態を変更することができなくなります。

### 重要なポイント

#### 1. 凍結後は破壊的操作が不可能

凍結された配列に対して、要素の追加、削除、変更などの破壊的操作を試みると `FrozenError` が発生します。

```ruby
a = [1, 2, 3]
a.freeze

# 以下の操作はすべて FrozenError を発生させる
a << 4              # FrozenError: can't modify frozen Array
a.push(4)           # FrozenError: can't modify frozen Array
a[0] = 10           # FrozenError: can't modify frozen Array
a.delete_at(0)      # FrozenError: can't modify frozen Array
a.clear             # FrozenError: can't modify frozen Array
```

#### 2. 非破壊的操作は可能

凍結された配列でも、新しい配列を返すメソッド（非破壊的メソッド）は正常に動作します。

```ruby
a = [1, 2, 3].freeze

# これらの操作は成功する（新しい配列を返す）
b = a + [4]         # => [1, 2, 3, 4]
c = a.map { |x| x * 2 }  # => [2, 4, 6]
d = a.select { |x| x > 1 }  # => [2, 3]

# 元の配列は変更されていない
a  # => [1, 2, 3]
```

#### 3. `freeze` は不可逆

一度凍結したオブジェクトを「解凍」する標準的な方法はありません。必要であれば、配列の複製（`dup` や `clone`）を作成する必要があります。

```ruby
a = [1, 2, 3].freeze

# 凍結された配列の複製を作成（複製は凍結されていない）
b = a.dup
b << 4  # => [1, 2, 3, 4] （成功）

# ただし dup は浅いコピー
c = [[1, 2], [3, 4]].freeze
d = c.dup
d << [5, 6]  # => [[1, 2], [3, 4], [5, 6]] （成功）
d[0] << 999  # 元の配列の要素が変更される（注意！）
```

### ユースケース

#### 1. 定数の保護

定数に代入された配列は、再代入はできませんが、内容の変更は可能です。`freeze` を使うことで、定数の内容も保護できます。

```ruby
# freeze なし
COLORS = ['red', 'green', 'blue']
COLORS << 'yellow'  # 成功してしまう（意図しない変更）

# freeze あり
COLORS = ['red', 'green', 'blue'].freeze
COLORS << 'yellow'  # FrozenError（意図通り）
```

#### 2. メソッドの戻り値の保護

メソッドが返す配列を外部から変更されたくない場合に使用します。

```ruby
class UserRepository
  ADMIN_USERS = ['alice', 'bob'].freeze

  def self.admin_users
    # 凍結された配列を返すことで、呼び出し側での変更を防ぐ
    ADMIN_USERS
  end
end

admins = UserRepository.admin_users
admins << 'charlie'  # FrozenError
```

#### 3. ハッシュのキーとして使用

凍結された配列は、ハッシュのキーとして安全に使用できます（内容が変更されないため）。

```ruby
cache = {}
key = [1, 2, 3].freeze
cache[key] = 'some value'

# キーが凍結されていないと、後から変更される危険がある
```

### 注意点

#### 1. 浅い凍結（Shallow Freeze）

`freeze` は対象のオブジェクト自体を凍結しますが、**その要素までは凍結しません**。

```ruby
a = [[1, 2], [3, 4]].freeze
a.frozen?       # => true
a[0].frozen?    # => false （要素は凍結されていない）

a[0] << 999     # 成功してしまう
a  # => [[1, 2, 999], [3, 4]]
```

すべての要素も凍結したい場合は、再帰的に `freeze` を呼ぶ必要があります。

```ruby
def deep_freeze(obj)
  obj.freeze
  obj.each { |element| deep_freeze(element) } if obj.is_a?(Array)
  obj
end

a = deep_freeze([[1, 2], [3, 4]])
a[0] << 999  # FrozenError
```

#### 2. パフォーマンスへの影響

凍結されたオブジェクトは、Ruby の VM によって最適化される場合があります。特に、文字列の凍結（`String#freeze`）はパフォーマンス向上に寄与することが知られています。

### 関連メソッド

- **`frozen?`**: オブジェクトが凍結されているかを確認
  ```ruby
  a = [1, 2, 3]
  a.frozen?  # => false
  a.freeze
  a.frozen?  # => true
  ```

- **`dup`**: オブジェクトの複製を作成（凍結状態はコピーされない）
  ```ruby
  a = [1, 2, 3].freeze
  b = a.dup
  b.frozen?  # => false
  ```

- **`clone`**: オブジェクトの複製を作成（凍結状態もコピーされる）
  ```ruby
  a = [1, 2, 3].freeze
  b = a.clone
  b.frozen?  # => true
  ```

### まとめ

`freeze` は、配列を不変にするための強力なメソッドです。定数の保護、メソッドの戻り値の保護、ハッシュのキーとしての使用など、様々な場面で活用できます。ただし、浅い凍結である点に注意し、必要に応じて再帰的な凍結を実装する必要があります。
