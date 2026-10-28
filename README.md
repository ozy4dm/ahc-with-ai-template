# AHC With AI template

長期 AHC の **With AI 部門** で使う実験記録と比較ツールの雛形です。AHC072 で使用した実験記録、結果一覧、スコアボード、ケース比較の仕組みから、問題に依存しない部分を取り出しました。解法、入力、公式テスタは含めていません。Human Only 部門には使用しないでください。

初めて使う方は [利用者向けガイド](docs/user-guide.md) を参照してください。設計と変更の根拠を追う場合は [設計・開発の履歴](docs/design-history.md) からコミットへ進めます。

## 新しい大会での準備

1. このリポジトリを大会用フォルダへ複製します。
2. 大会ページの「部門」タブで With AI 部門を選び、[20261026版の公式ルール](https://info.atcoder.jp/entry/long-ahc-llm-rules-ja)と、その大会に適用される条件を確認します。`AGENTS.md` はこの部門用の作業指示です。Claude Code では `CLAUDE.md` がこれを読み込みます。ルールが更新された場合は、指示とスキル・プロンプトの整合性を確認してください。
3. 参加者がブラウザで問題文と公式 generator / tester / visualizer を取得し、問題文 PDF は `local/problem.pdf`、公式ツールは `tools/` に置きます。両ディレクトリは Git の管理対象から除外しています。公式配布物のライセンスと公開条件を確認してください。
4. `config.example.json` を `config.json` にコピーし、入力、スコアの向き、ソルバーと採点コマンドを設定します。Windows では実行ファイル名に `.exe` を付けます。実行ファイルの相対パスは `/` 区切りのままでよく、リポジトリのルートから解決されます。
5. 入力を `cases/` の該当する集合へ置き、`docs/experiments/TEMPLATE.md` を複製して評価条件を先に記録します。

`config.json`、`local/`、`tools/` は大会ごとのローカルファイルなので Git の管理対象から外しています。共通の比較・集計スクリプトは `scripts/` にあります。公開時は大会固有の入力、出力、ログ、解法を含めるか個別に確認してください。

## 比較実行

`config.json` の `solvers` と `scorer` はコマンドを文字列配列で指定します。ソルバーは入力を標準入力で受け取り、出力を標準出力へ書きます。採点コマンドの `{input}` と `{output}` はファイルパスに置換され、`score_pattern` の最初の括弧グループから数値を読み取ります。コマンドはシェルを通さず実行されます。

```text
python scripts/compare_solvers.py EXP-001 baseline candidate
python scripts/show_scoreboard.py --direction lower
powershell -NoProfile -ExecutionPolicy Bypass -File scripts/show_experiment_results.ps1
```

比較結果は `reports/experiments/EXP-xxx/` に保存されます。同じ ID の結果は上書きしません。`show_scoreboard.py` は全ケースが揃った版だけを順位表へ載せます。AI に結果の分析や採用案の提案も依頼できます。判断の経緯は `docs/decisions.md` に記録します。

## 構成

| パス | 用途 |
|---|---|
| `cases/smoke/` | 入出力と不変条件の確認 |
| `cases/dev/` | 開発中の繰り返し評価 |
| `cases/validation/` | 事前に固定した検証集合 |
| `cases/regression/` | 過去に問題が出たケース |
| `docs/experiments/` | 実験計画と記録 |
| `docs/solutions.md` | 解法版の台帳 |
| `docs/decisions.md` | 提案者・判断者を含む採用・棄却の記録 |
| `reports/experiments/` | 実行結果 |
| `src/bin/` | 大会ごとの解答コード |
| `scripts/` | 共通の比較・集計スクリプト |
| `tools/` | 手動取得した大会公式ツール（Git 管理対象外） |

AHC072 固有の解法、公式ツール、生成入力、診断用スクリプトは移していません。特に採点式、時間制限、スコアの向きは大会ごとに設定してください。

## Claude Code で使う

リポジトリのルートで Claude Code を起動します。`CLAUDE.md` には共通の作業指示、`AGENTS.md` には With AI 部門用の境界があります。起動後に `/context` で読み込みを確認してください。`.claude/skills/` の `/ahc-setup`、`/ahc-implement`、`/ahc-experiment` は手動スキルです。記入例は [prompts/README.md](prompts/README.md) にあります。

With AI 部門では解法の考案、実装、実験、結果に基づく改善も AI に依頼できます。ただし、他者が関わった非公開モデル・ハーネスの利用禁止と、通常の AHC ルールは引き続き適用されます。大会固有のルールも確認してください。

## ライセンス

このリポジトリのテンプレート本体は [MIT License](LICENSE) で公開します。各大会で追加する公式配布物など、第三者が権利を持つファイルには、それぞれのライセンスや公開条件が適用されます。
