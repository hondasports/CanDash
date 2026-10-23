# CanDash

2022年式 MAZDA3（BP系 / SKYACTIV-G 2.0）の車両データを、M5Stack を使って取得・記録するための検証プロジェクト。

現時点ではドキュメントのみ。実装は段階的に進める。

## 目的

1. OBD-II ポートから CAN のブロードキャストが見えるのかを確定する（受信専用で記録）
2. OBD-II 経由の診断要求（標準 PID / UDS）でどの値が取れるかを確認する
3. 上記の結果をもとに、データ取得の方法を決める

## ドキュメント

| ドキュメント | 内容 |
|---|---|
| [docs/research/mazda3-bp-obd-can.md](docs/research/mazda3-bp-obd-can.md) | MAZDA3 BP の OBD / CAN に関する事前調査 |
| [docs/design/architecture.md](docs/design/architecture.md) | M5Stack ロガーの構成設計 |
| [docs/verification-plan.md](docs/verification-plan.md) | 実車での検証手順と結果の判定方法 |

## 構成の概要

- 本体: M5Stack CoreS3
- CAN トランシーバー: M5Stack CAN Unit（U085, CA-IS3050G）
- 記録: microSD に candump 形式で保存
- 操作: Android Chrome から Web Bluetooth で操作（本体タッチ操作も可能）
- 開発環境: PlatformIO + Arduino + M5Unified

## 進め方

| ステップ | 内容 | 状態 |
|---|---|---|
| 1 | 受信専用モード + SD 保存 + 本体タッチ UI + サマリ出力 | 未着手 |
| 2 | BLE 操作 + Web Bluetooth ページ + 時刻同期 | 未着手 |
| 3 | 標準 PID ポーリングモード | 未着手 |
| 4 | （任意）UDS 探索、Wi-Fi AP でのファイル回収 | 未着手 |

## 注意事項

- 車両の CAN バスへの送信は、車両の動作に影響する可能性がある。既定は受信専用とし、送信を伴うモードは明示的に切り替えたときだけ有効にする。
- 操作は停車中に行う。
- 本プロジェクトの内容は個人の検証目的であり、動作や安全性を保証しない。
