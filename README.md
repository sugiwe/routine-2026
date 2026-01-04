# 2026 年 習慣管理プロジェクト

毎日コツコツ取り組む習慣を記録・管理するためのリポジトリです。

Claude Code を活用して、日々の習慣記録と学習ノートを効率的に管理しています。

## 特徴

- **日次記録と学習ノートの分離管理**: 日々の実施記録は `daily/` で時系列管理、学習内容は `learning/` で体系的に蓄積
- **Claude Code による効率化**: 学習内容の記録時に、自動的に詳細ノートを作成し、深掘り情報を追加
- **連番管理による進捗の可視化**: 学習ノートは連番で管理し、進捗が一目でわかる
- **Git + GitHub で継続を可視化**: 毎日の記録をコミット・PR で管理し、習慣化をサポート

## 管理している習慣

### 基本習慣

- ラジオ体操
- シダキュア（花粉症の薬）
- スクワット（目標: 20 回）
- 筋トレ（腕立て伏せ、腹筋 各 20 回）
- Typnix 練習（タイピング練習アプリ）

### 学習習慣

- **Sociomedia ヒューマンインターフェース ガイドライン**（略称: shig）
  - 100 トピックを 1 日 1 つずつ読んでいく
  - https://www.sociomedia.co.jp/category/shig
- **Documentation for Ruby 4.0 - Array Instance Methods**（略称: Ruby）
  - class Array の Instance Methods を 1 日 1 つずつ読んでいく
  - https://docs.ruby-lang.org/en/4.0/Array.html

## Web ページで閲覧

このリポジトリは Docsify を使って Web ページとして閲覧できます。

**📖 [https://sugiwe.github.io/routine-2026/](https://sugiwe.github.io/routine-2026/)**

- サイドバーで日次記録や学習ノートに簡単アクセス
- 検索機能で過去の記録を素早く検索
- Markdown が見やすく整形された状態で表示

## ディレクトリ構成

```
2026/
├── README.md           # このファイル
├── CLAUDE.md           # Claude Code 向けのプロジェクトガイド
├── index.html          # Docsify 設定（Web ページ化）
├── _sidebar.md         # サイドバーのナビゲーション
├── .nojekyll           # GitHub Pages 用
├── daily/              # 日次記録
│   ├── TEMPLATE.md     # 日次ファイルのテンプレート
│   ├── 2026-01-01.md
│   ├── 2026-01-02.md
│   └── ...
└── learning/           # 学習ノート（連番管理）
    ├── ruby-array-methods/
    │   ├── 001-ampersand.md
    │   ├── 002-asterisk.md
    │   └── ...
    └── sociomedia-hig/
        ├── 001-simple.md
        ├── 002-easy.md
        └── ...
```

## 使い方

### セットアップ

1. このリポジトリをフォークまたはクローン
2. `CLAUDE.md` を編集して、自分の習慣に合わせてカスタマイズ
3. `daily/TEMPLATE.md` を自分の習慣に合わせて編集
4. Git で管理を開始

```bash
git init
git add .
git commit -m "Initial setup"
```

### 日々の記録

#### 手動で記録する場合

1. `daily/TEMPLATE.md` をコピーして、その日の日付でファイルを作成
2. チェックボックスで実施状況を記録
3. 学習内容はメモ欄に記入

#### Claude Code を使う場合

Claude Code を起動して、以下のように話しかけるだけ：

```
「今日の記録を開始、ラジオ体操とシダキュアは完了」
「スクワット 20 回完了」
「shigの『一貫性の原則』を読んだ。メモ: UI は統一感が大事」
「Rubyの Array#append を読んだ。メモ: 配列の末尾に要素を追加」
```

Claude Code が自動的に：

- `daily/` に日次記録を作成・更新
- `learning/` に詳細な学習ノートを作成（Ruby の場合は深掘り情報も追加）

詳しい使い方は [CLAUDE.md](CLAUDE.md) を参照してください。

## カスタマイズ方法

### 自分の習慣に合わせる

1. **CLAUDE.md を編集**
   - 管理する習慣のリストを変更
   - 学習習慣のセクションを自分の学習内容に置き換え
2. **daily/TEMPLATE.md を編集**
   - 基本習慣の項目を追加・削除
   - 学習習慣のセクションを変更
3. **learning/ のディレクトリ構成を変更**
   - 自分の学習テーマに合わせてディレクトリを作成

### 運用方法のカスタマイズ

- **ブランチ運用**: 毎日ブランチを切って PR を作成する運用も可能
- **振り返り**: 週次・月次で達成率を振り返るスクリプトを追加
- **自動化**: GitHub Actions で統計を自動生成するなど

## このリポジトリを参考にする場合

同じように習慣管理をしたい方は、以下の手順で始められます：

1. このリポジトリをテンプレートとして使用
2. `CLAUDE.md` と `daily/TEMPLATE.md` を自分の習慣に合わせて編集
3. 不要なファイル（`daily/2026-01-*.md`、`learning/` 配下）を削除
4. 自分の習慣記録を開始

## ライセンス

MIT License

自由に使用・改変・配布してください。
