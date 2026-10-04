# テンプレートの選び方とコピー手順

| 項目 | 内容 |
| --- | --- |
| 文書ID | GUIDE-TEMPLATES |
| 状態 | 有効 |
| この文書に書くこと | 目的に合うテンプレートの選択、コピー後の修正、文書一覧への登録方法 |
| 依存先 | [ファイルの整頓ルール](../docs/guide/file-organization.md)：配置先と命名規則 |
| 関連資料 | [開発の進め方](../docs/guide/workflow.md)、[文書一覧](../docs/README.md) |

## 何を使うか

| やりたいこと | コピー元 | コピー先 |
| --- | --- | --- |
| 機能を仕様から実装・検証まで進める | [feature/](feature/spec.md) の4ファイル | `docs/features/FEAT-001-short-name/` |
| まず小さく試し、採用するか判断する | [experiment.md](experiment.md) | `experiments/EXP-001-short-name/README.md` |
| 後から理由を知りたい設計判断を残す | [decision.md](decision.md) | `docs/decisions/ADR-001-short-name.md` |

`001` は未使用の番号、`short-name` は内容がわかる英小文字とハイフンの名前に置き換えます。元のテンプレートは残し、コピーした文書に記入します。必要のない見出しには「対象外：理由」を書けば十分です。

## 機能の4ファイルをまとめてコピーする

リポジトリのルートで次を実行します。`FEAT-001-short-name` は作成する機能の名前に変えてください。既存フォルダを上書きしない手順です。

```powershell
$featureDir = 'docs/features/FEAT-001-short-name'
if (Test-Path -LiteralPath $featureDir) {
    throw "コピー先が既にあります: $featureDir"
}
New-Item -ItemType Directory -Path $featureDir | Out-Null
Copy-Item -Path 'templates/feature/*.md' -Destination $featureDir

# コピー先から全体文書への相対リンクに変更します。
Get-ChildItem -LiteralPath $featureDir -Filter '*.md' | ForEach-Object {
    $featureText = Get-Content -LiteralPath $_.FullName -Raw -Encoding UTF8
    $featureText = $featureText.Replace('../../docs/product/', '../../product/')
    $featureText = $featureText.Replace('../../docs/architecture/', '../../architecture/')
    Set-Content -LiteralPath $_.FullName -Value $featureText -Encoding UTF8
}
```

手動の場合も `templates/feature/` の4ファイルを同じフォルダにコピーします。全体要件へのリンクを `../../product/requirements.md`、全体設計へのリンクを `../../architecture/overview.md` に変更してください。同じフォルダ内の `spec.md` などへのリンクはそのまま使えます。

| ファイル | 記入する内容 | 内容の前提になる文書 |
| --- | --- | --- |
| [spec.md](feature/spec.md) | 目的、要件、受入条件 | 全体要件 |
| [design.md](feature/design.md) | 仕様を実現する構成と処理 | 機能仕様、全体設計 |
| [tasks.md](feature/tasks.md) | 実装作業、完了条件、検証方法 | 機能仕様、機能設計 |
| [verification.md](feature/verification.md) | 受入条件ごとの検証箇所と実行結果 | 機能仕様、作業計画 |

## コピー後の仕上げ

1. タイトルと `FEAT-XXX`・`EXP-XXX`・`ADR-XXX` を実際の名前・番号に置き換えます。機能文書のIDは `FEAT-001-SPEC`、要件は `FEAT-001-R01` のように統一します。
2. 状態を「テンプレート」から「下書き」に変えます。内容の前提が揃い、採用した文書を「有効」にします。使わなくなった文書は「廃止」とし、後継文書や廃止理由を残します。
3. メタデータの「依存先」に、前提とする文書のリンクと依存する内容を書きます。「関連資料」は補足を探すためのリンクです。こちらに置いた資料を必須の前提として扱わないでください。
4. 記入指針を実際の内容に置き換えます。わからないことは「未決」、実行していない検証は「未実施」と記録します。コマンドや結果を推測で埋めないでください。
5. 機能は [機能一覧](../docs/features/README.md)、決定記録は [決定記録一覧](../docs/decisions/README.md)、試作は [試作一覧](../experiments/README.md) に登録します。新しい文書の種類を増やした場合は [文書一覧](../docs/README.md) にも役割と依存先を追加します。

`experiment.md` と `decision.md` のリンク候補はバッククォートで示しています。コピー後に実在する文書を選び、**コピー先からの相対パス**でMarkdownリンクにしてください。文書の移動・削除時の扱いは [ファイルの整頓ルール](../docs/guide/file-organization.md) を参照します。
