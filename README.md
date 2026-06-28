# lucidoc

明晰で「伝わる」ドキュメントを書くための [Claude Code](https://claude.com/claude-code) スキル集。

**「いいドキュメント = 読み手が効率よく目的を達成できる（ISO 9241-11）」** を判断基準に、読み手の選定からテーマ分解・アウトライン・文章・文の推敲、そして成果物のレビューと自動修正までを一貫した手順にしたスキルをまとめています。各スキルは、白紙のサブエージェントに実際に使わせて両面評価する empirical な手法でチューニング済みです。

## 収録スキル

| ファイル | スキル名 | 何をするか |
|---|---|---|
| `skills/clear-document-writing/SKILL.md` | `clear-document-writing` | 6ステップ（読み手選定 → テーマ分解 → アウトライン → 文章構成 → 文 → レビューと自動修正）でドキュメントを作成。要点先行・図表優先・必要な情報だけ、を全工程で貫く。Step 6 のレビュー修正は `doc-review-fix-loop` に委譲し、作成からレビュー修正まで一貫 |
| `skills/doc-review-fix/SKILL.md` | `doc-review-fix` | ドキュメント成果物を1パスでレビューして自動修正する。白箱（ルーブリック採点）と黒箱（初見の読み手サブエージェント）を並列 dispatch し、問題を「自動修正／情報補充／捏造リスク（要確認で残置）」に分類して直せるものをその場で直し、対応サマリと収束判定をレポート出力 |
| `skills/doc-review-fix-loop/SKILL.md` | `doc-review-fix-loop` | `doc-review-fix` を CONVERGED まで繰り返す自動レビュー修正ループ。各パスでレビュー→自動修正し、修正で内容が変わるたび新しい目で再レビュー。critical/major がゼロに収束するか上限回数で打ち切り、最終レポートを出力 |
| `skills/feature-design-doc/SKILL.md` | `feature-design-doc` | 複数システムにまたがる機能の設計書を、調査 → 実現可能性判定 → 設計判断の収集 → 設計書作成 → 理解度検証 の5ステップで作成 |

## 使い方

このリポジトリは Claude Code のスキルディレクトリ構成（`skills/<スキル名>/SKILL.md`）に従っています。各 `SKILL.md` は frontmatter に `name` / `description` を持ち、ディレクトリ名は `name` と一致します。

```
lucidoc/
├── README.md
└── skills/
    ├── clear-document-writing/    # ドキュメント作成（6ステップ）
    │   └── SKILL.md
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
cp -r lucidoc/skills/clear-document-writing /path/to/your-project/.claude/skills/
```

配置後、Claude Code で `/clear-document-writing` のように呼び出せます（または「ドキュメントを書いて」等のトリガーで自動起動）。

> リポジトリ全体を Claude Code プラグインとして配布したい場合は `.claude-plugin/plugin.json` を追加します（未追加）。

## ライセンス

未定（配布前に設定）。
