# Cage（Foxbook改造）現状 ToDo

更新日：2026-10-26

## 現状

- Foxbookで必要な改造は、v0.2.0系を親とする`fix/touch-output-transform`のコミット`e7e88b1`のみである。
- `seat.c`のタッチdown／motion座標を、出力transformが`90`、`180`、`270`の場合に補正する。
- `/home/tuyomori/project/cage`は`fix/touch-output-transform`をチェックアウトし、改造コミット`e7e88b1`とFoxbook文書コミット`87f90ab`を含む。`cage-v0.2.0`は比較用作業コピーであり、管理上のリポジトリ名ではない。

## 未完了

- [ ] Surfaceの単一出力で、`normal`、`90`、`180`、`270`の各transform時にタッチdownとdragの座標が画面表示と一致することを確認する。
- [ ] `wlr-randr`での向き変更とfoxbook-controlの回転操作を通した連続確認を行う。
- [ ] 複数出力では先頭出力だけを使う実装であるため、複数出力環境を非対応として維持するか、出力対応を設計する。
- [ ] 現行`master`へ改造を移植する必要が生じた場合、v0.2.0系との大きな上流差分を踏まえて再実装・ビルド・実機検証を行う。単純なcherry-pickはしない。

## 参照

- 現行仕様：[仕様書 v0.1](specification/foxbook-cage仕様書v0.1.md)
- 横断契約：[`IF-CTL-CAGE-001`](../foxbook/docs/interfaces/foxbook-control--cage.md)
