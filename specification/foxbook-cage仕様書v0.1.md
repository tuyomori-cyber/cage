# Cage（Foxbook改造）仕様書 v0.1

- 文書バージョン：0.1
- 作成日：2026-10-26
- 状態：コード差分ベース
- 対象：Cage v0.2.0系、コミット`e7e88b1 Fix touch coordinates after output rotation`

## 1. 位置付け

FoxbookはSurface上のCage／WaylandセッションでFirefoxを使う。画面を回転した時にも、タッチ座標が表示上の位置と一致することがこの改造の目的である。

正規のリポジトリ名は`cage`である。現在の`cage`作業ツリーは上流追従の`master`（`ef6ef6a`）で、改造自体はユーザーForkの`origin/fix/touch-output-transform`にある`e7e88b1`へ記録されている。`cage-v0.2.0`は改造コミットをチェックアウトした比較用作業コピーであり、管理上のリポジトリ名ではない。

## 2. 改造の基準と範囲

`e7e88b1`の親はCage v0.2.0タグのコミット`e128a9f`である。変更対象は`seat.c`だけで、46行追加・2行変更である。現在の`master`との全差分には上流世代差が含まれるため、Foxbook改造の差分として扱わない。

## 3. 実装内容

`transform_touch_coords()`を追加し、最初の出力の`wlr_output->transform`を参照して、タッチの正規化座標`(x, y)`を次のように補正する。

| transform | 補正後 |
| --- | --- |
| `normal`ほか | `(x, y)` |
| `90` | `(1 - y, x)` |
| `180` | `(1 - x, 1 - y)` |
| `270` | `(y, 1 - x)` |

補正後の座標を`wlr_cursor_absolute_to_layout_coords()`へ渡す。適用箇所は`handle_touch_down()`と`handle_touch_motion()`であり、タッチ開始とドラッグ中の両方を対象にする。ポインター、キーボード、出力設定、Cageログアウト処理は変更しない。

## 4. 制約と安全境界

- 単一出力キオスクを前提とする。出力リストの先頭だけを読み、複数出力への対応はしない。
- 出力が存在しない場合、または未知のtransformでは入力座標を変更しない。
- この改造はCageの画面回転設定そのものを変更しない。向きの取得・変更はfoxbook-controlが`wlr-randr`を通じて行う。
- `cage_logout`はCageのコードを変更せず、foxbook-controlが`FOXBOOK_CAGE_SESSION=1`を条件に呼出元ログインセッションを終了する。

## 5. 横断契約と検証

横断上の正本は[`IF-CTL-CAGE-001`](../../foxbook/docs/interfaces/foxbook-control--cage.md)である。対象出力はfoxbook-control側で`eDP-1`、許容transformは`normal`と`90`に限定される。

コード差分は確認済みだが、CageのビルドおよびSurface実機での各方向のタッチ座標、drag、foxbook-control経由の回転連携は未検証である。確認項目は[TODO](../TODO.md)で管理する。
