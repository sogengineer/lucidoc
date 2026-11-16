# lucidoc

明晰で「伝わる」ドキュメントを書くための [Claude Code](https://claude.com/claude-code) スキル集。

**「いいドキュメント = 読み手が効率よく目的を達成できる（ISO 9241-11 の有効性・効率に対応）」** を判断基準に、読み手の選定からテーマ分解・アウトライン・文章・文の推敲、そして成果物のレビューと自動修正までを一貫した手順にしたスキルをまとめています。各スキルは、白紙のサブエージェントに実際に使わせて両面評価する empirical な手法でチューニング済みです。

## 収録スキル

| ファイル | スキル名 | 何をするか |
|---|---|---|
| `skills/clear-document-writing/SKILL.md` | `clear-document-writing` | 6ステップ（読み手選定 → テーマ分解 → アウトライン → 文章構成 → 文 → レビューと自動修正）でドキュメントを作成。要点先行・図表優先・必要な情報だけ、を全工程で貫く。Step 6 のレビュー修正は `doc-review-fix-loop` に委譲し、作成からレビュー修正まで一貫 |
| `skills/doc-review-fix/SKILL.md` | `doc-review-fix` | ドキュメント成果物を1パスでレビューして自動修正する。白箱（ルーブリック採点）と黒箱（初見の読み手サブエージェント）を並列 dispatch し、問題を「自動修正／情報補充／捏造リスク（要確認で残置）」に分類して直せるものをその場で直し、対応サマリと収束判定をレポート出力 |
| `skills/doc-review-fix-loop/SKILL.md` | `doc-review-fix-loop` | `doc-review-fix` を CONVERGED まで繰り返す自動レビュー修正ループ。各パスでレビュー→自動修正し、修正で内容が変わるたび新しい目で再レビュー。critical/major がゼロ（または残りが要確認のみ）に収束するか上限回数で打ち切り、最終レポートを出力 |
| `skills/feature-design-doc/SKILL.md` | `feature-design-doc` | 複数システムにまたがる機能の設計書を、調査 → 実現可能性判定 → 設計判断の収集 → 設計書作成 → セルフチェック（理解度検証＋ギャップ検出）の5ステップで作成 |

## 使い方

このリポジトリは Claude Code のスキルディレクトリ構成（`skills/<スキル名>/SKILL.md`）に従っています。各 `SKILL.md` は frontmatter に `name` / `description` を持ち、ディレクトリ名は `name` と一致します。

```
lucidoc/
├── README.md
└── skills/
    ├── clear-document-writing/    # ドキュメント作成（6ステップ）
    │   ├── SKILL.md               # ワークフロー＋原則（本体）
    │   └── references/            # 実行時に Read する詳細資料
    │       ├── explanation-patterns.md  # 章・パラグラフの中身の組み方（伝わる説明の型）
    │       ├── sentence-refinement.md   # 文の推敲・図表化の基準と実例
    │       └── common-failures.md       # よくある失敗チェックリスト（白箱ルーブリック兼用）
    ├── doc-review-fix/            # レビュー＆自動修正（1パス）
    │   └── SKILL.md
    ├── doc-review-fix-loop/       # 収束までループ（clear-document-writing の Step 6 の実体）
    │   └── SKILL.md
    └── feature-design-doc/        # 機能設計書（5ステップ）
        └── SKILL.md
```

> `clear-document-writing` の Step 6 は `doc-review-fix-loop` に委譲します。作成からレビュー修正まで一貫させたい場合は、この3つ（`clear-document-writing` / `doc-review-fix` / `doc-review-fix-loop`）をまとめて配置してください。レビュー修正だけ単独で使うこともできます。

利用するには、使いたいスキルのディレクトリを自分のプロジェクトの `.claude/skills/`（または `~/.claude/skills/`）配下にコピーします:

```bash
# 単体で使う場合（例: レビュー修正1パスだけ）
cp -r lucidoc/skills/doc-review-fix /path/to/your-project/.claude/skills/

# 作成からレビュー修正まで一貫させる場合（推奨: 3スキルをまとめて配置）
cp -r lucidoc/skills/clear-document-writing \
      lucidoc/skills/doc-review-fix \
      lucidoc/skills/doc-review-fix-loop \
      /path/to/your-project/.claude/skills/
```

配置後、Claude Code で `/clear-document-writing` のように呼び出せます（または「ドキュメントを書いて」等のトリガーで自動起動）。

## プラグインとして導入する

このリポジトリは Claude Code プラグインとしても配布しています（`.claude-plugin/plugin.json` と `.claude-plugin/marketplace.json` を同梱）。個別のスキルをコピーする代わりに、まとめて導入・更新できます。

```bash
# 1. マーケットプレイスを登録（初回のみ）
/plugin marketplace add sogengineer/lucidoc

# 2. プラグインをインストール
/plugin install lucidoc@lucidoc
```

インストール後、各スキルは `lucidoc` 名前空間付きで呼び出せます（トリガー文言での自動起動も有効）:

```
/lucidoc:clear-document-writing
/lucidoc:doc-review-fix
/lucidoc:doc-review-fix-loop
/lucidoc:feature-design-doc
```

更新を配布するときは `.claude-plugin/plugin.json` の `version` を上げてコミットしてください（`version` を固定したまま新しいコミットを push しても利用者には更新が届きません）。

## 推奨する併用スキル: yomiyasu

[yomiyasu](https://github.com/nanaism/yomiyasu) が導入されていなければ、あわせて導入してください。yomiyasu は、意味と条件を保ったまま日本語を読みやすく整える推敲スキルです。lucidoc は次の工程で、書いた・直した日本語を yomiyasu にかけます。

- `clear-document-writing` の Step 5（文を書く）
- `doc-review-fix` の修正適用（`doc-review-fix-loop` からも同じ）
- `feature-design-doc` の設計書の散文

yomiyasu が無くても lucidoc は止まりません。その場合は lucidoc 内の推敲基準だけで進め、「yomiyasu 未使用」を報告に残します。

Claude Code（プラグインとして導入）:

```bash
# 1. マーケットプレイスを登録（初回のみ）
/plugin marketplace add nanaism/yomiyasu

# 2. プラグインをインストール
/plugin install yomiyasu@yomiyasu
```

Codex（GitHub CLI の `gh skill` で導入）:

```bash
gh skill install nanaism/yomiyasu yomiyasu --agent codex
```

導入方法によってスキル名が変わります。Claude Code のプラグインでは `yomiyasu:yomiyasu`、それ以外（Codex など）では `yomiyasu` です。lucidoc はどちらの名前でも呼べるように書いてあります。ほかの導入方法は yomiyasu の README を参照してください。

## ライセンス

[MIT License](./LICENSE) © 2026 sogengineer
