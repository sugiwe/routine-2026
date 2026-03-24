# Array#shelljoin

- 日付: 2026-03-24
- URL: https://docs.ruby-lang.org/en/4.0/Array.html#method-i-shelljoin
- daily 記録: [daily/2026-03-24.md](../../daily/2026-03-24.md)

## 基本情報

```ruby
shelljoin -> String
```

配列の各要素を Bourne シェルのコマンドライン中で安全に使えるためのエスケープを適用し、空白文字で連結したコマンドライン文字列を生成するメソッドです。

**注**: このメソッドを使用するには `require 'shellwords'` が必要です。

## ユーザーの学習記録（daily より）

### 第一印象のメモ

初めて聞いたし、 https://docs.ruby-lang.org/ja/latest/method/Shellwords/m/shelljoin.html を読んでも全然わからない。シェル用に使いやすい形に変換するみたいなことかな？？

### コード例

```ruby
# Builds a command line string from an argument list array joining all elements escaped for the Bourne shell and separated by a space.
# 引数リストの配列からコマンドライン文字列を生成します。各要素をBourneシェル用にエスケープ処理し、スペースで連結します。
```

## 深掘り・補足

### 何をするメソッドか

`shelljoin` は、配列の要素をシェルコマンドとして安全に実行できる文字列に変換するメソッドです。特に、**スペースや特殊文字を含む引数を安全にエスケープする**ことが主な役割です。

### 具体的な使用例

```ruby
require 'shellwords'

# 基本的な使い方
pattern = 'Jan 15'
file = 'file name with spaces'
command = ['grep', pattern, file].shelljoin
puts command
# => grep Jan\\ 15 file\\ name\\ with\\ spaces

# システムコマンドとして実行
system(command)
# これは以下と同じ意味:
# grep "Jan 15" "file name with spaces"
```

### 重要なポイント

1. **自動エスケープ**
   - スペースを含む文字列は自動的にエスケープされる
   - 手動でクォートやエスケープを追加する必要がない

2. **安全性**
   - シェルインジェクション攻撃を防ぐための重要なメソッド
   - 外部入力（ユーザー入力、ファイル名など）をシェルコマンドに含める際に必須

3. **Shellwords モジュールが必要**
   - `require 'shellwords'` を事前に実行する必要がある

### シェルインジェクション対策の例

```ruby
require 'shellwords'

# 危険な例（シェルインジェクションの脆弱性）
user_input = "file.txt; rm -rf /"
command = "cat #{user_input}"
# system(command) # 実行すると危険！

# 安全な例（shelljoin を使用）
files = ['file.txt', user_input]
command = ['cat'].concat(files).shelljoin
puts command
# => cat file.txt file.txt\;\ rm\ -rf\ /
# セミコロンがエスケープされ、コマンドインジェクションを防ぐ
```

### 注意点

1. **require が必要**
   - Array クラスのメソッドだが、Shellwords モジュールを読み込まないと使えない

2. **Windows での互換性**
   - Bourne シェル（sh, bash など）向けのエスケープ処理
   - Windows のコマンドプロンプト（cmd.exe）では適切に動作しない可能性がある

3. **複雑なコマンドには向かない**
   - パイプ（`|`）やリダイレクト（`>`）を含む複雑なコマンドには不向き
   - そのような場合は `Open3.popen3` などの使用を検討

### 関連メソッド

- **`Shellwords.shellescape`**: 単一の文字列をエスケープ
  ```ruby
  require 'shellwords'
  Shellwords.shellescape("file name with spaces")
  # => "file\\ name\\ with\\ spaces"
  ```

- **`Shellwords.shellsplit`**: シェルコマンド文字列を配列に分割（shelljoin の逆）
  ```ruby
  require 'shellwords'
  Shellwords.shellsplit("grep Jan\\ 15 file\\ name")
  # => ["grep", "Jan 15", "file name"]
  ```

### 実践的なユースケース

1. **外部コマンドの安全な実行**
   ```ruby
   require 'shellwords'

   files = Dir.glob('*.txt')
   command = ['wc', '-l'].concat(files).shelljoin
   result = `#{command}`
   ```

2. **ユーザー入力を含むコマンド生成**
   ```ruby
   require 'shellwords'

   search_term = params[:query] # ユーザー入力
   files = params[:files]
   command = ['grep', '-r', search_term].concat(files).shelljoin
   system(command)
   ```

3. **ログ記録用のコマンド文字列生成**
   ```ruby
   require 'shellwords'

   command_parts = ['ffmpeg', '-i', 'input file.mp4', '-o', 'output.mp4']
   puts "Executing: #{command_parts.shelljoin}"
   # => Executing: ffmpeg -i input\ file.mp4 -o output.mp4
   ```

### まとめ

`shelljoin` は、配列をシェルコマンド文字列に変換する際の「安全な橋渡し役」です。特に外部入力を含むコマンドを実行する際は、セキュリティ上の理由から積極的に使用すべきメソッドです。

「シェル用に使いやすい形に変換する」という第一印象は正しく、さらに「**セキュリティ的に安全な形に変換する**」という重要な側面があります。
