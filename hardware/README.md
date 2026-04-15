# Hardware Data Layout

このディレクトリは `NucleoMouse2023` の回路・基板製造データを保管する場所です。

- `cad/`: CAD元データ（`.sch`, `.brd`, PDF など）
- `gerber/`: 発注用ガーバー出力（ZIP）

## CubeIDE運用との関係

- `CubeIDE` のビルド対象は `.cproject` の `sourceEntries` で `Core/` と `Drivers/` のみです。
- そのため `hardware/` 配下はビルド・リンク対象になりません。
- プロジェクト直下の日本語フォルダ名を避け、OS差異（特に Windows の文字化け/正規化差）を減らすために英数字パスへ整理しています。
