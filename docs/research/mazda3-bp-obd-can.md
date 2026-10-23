# MAZDA3 BP の OBD / CAN 事前調査

調査日: 2026-10-24
対象: 2022年式 MAZDA3（BP系、SKYACTIV-G 2.0）

本書は Web 上の公開情報を調べた結果である。実車での確認はまだ行っていない。
海外（英語・ドイツ語）の情報が多く、国内の情報は少なかった。

## 結論

- OBD-II ポートから CAN のブロードキャストを直接記録することは、できない可能性が高い。
  - 2019年式 BP の実測例で、OBD ポートは通常時に静かであり、ゲートウェイの奥にある診断用 CAN のみが出ていると報告されている。
- 診断要求（標準 PID、Mazda 独自 PID / UDS）であれば、値を取得できる可能性が高い。
- 生の CAN を記録したい場合は、車内配線（ゲートウェイの内側）を分岐して接続する必要があると推定する。

## 区分ごとの整理

### 確認済み（公開情報の記述として確認したもの）

| 項目 | 内容 | 出典 |
|---|---|---|
| OBD ポートの挙動 | 2019年式 BP では、旧型 BM と同じ方法で OBD ポートから CAN を記録できなかった | [1] [2] |
| CAN FD の可能性 | 測定者は 50MHz でサンプリングしており、CAN FD の取りこぼしは考えにくいとしている | [1] |
| ゲートウェイ | 測定者は、新型ではゲートウェイが追加され、OBD には診断用 CAN のみが出ていると結論づけている | [1] |
| openpilot の対応 | 2019〜2023年の Mazda 3 / CX-30 / CX-50 は新しい運転支援システムのため未対応。純正の comma コネクタは合わず、コミュニティ製の開発用ハーネスがある | [3] |
| 車内のタップ位置 | 市販の CAN アダプタが Mazda 3 IV BP（2019〜）に対応しており、運転席足元のコネクタ（足置きの約 30cm 上）を接続位置としている | [4] |
| 兄弟車の例 | CX-30 では左キックパネルの 2 つのコネクタが CAN の接続位置とされている | [5] |
| 診断ソフト | ディーラーは 2019年の新型 Mazda 3 以降、Bosch 系の診断ソフト MDARS へ移行している | [6] |
| サードパーティ対応 | FORScan は Mazda の第7世代（新型 Mazda 3、CX-30 など）を部分対応または未対応としている | [7] |
| 独自 PID の例 | CX-5 でタイヤ空気圧の独自 PID（ヘッダ 720、PID 222A05〜222A08）が使われている | [8] |
| 世代差 | 新しい世代では一部の制御が PCM 内に移り、旧型向け PID のアドレスが変わっている例がある | [8] |

### 推定（未確認。確定させる方法）

| 推定 | 確定させる方法 |
|---|---|
| 2022年式も OBD は診断用 CAN のみ | OBD 6/14 番ピンを受信専用 500kbps で記録し、ブロードキャストがないことを確認する |
| 標準 PID（モード 01）は応答する | `7DF` に `02 01 0C` などを送り、`7E8` の応答を確認する |
| PCM は UDS（`22xxxx`）に応答する | 標準 PID の確認後、既知の PID から試す |
| 運転席足元のコネクタにブロードキャストが流れている | 配線図の確認、または受信専用での実測 |
| 日本仕様も海外仕様と同じ構成 | 実車での確認 |

### 未確認

- 2022年式 MAZDA3 2.0 での実測ログ
- BP 世代向けの公開 DBC ファイル（今回の調査では見つからなかった）
- 日本仕様（BP5P / BP8P / BPFP など）のゲートウェイ構成
- 運転席足元コネクタのピン配置と、年式による違い
- CX-5 の独自 PID が BP で使えるか

## データ取得方法の比較

| 方法 | 取得できる値 | リスク | 難易度 |
|---|---|---|---|
| OBD + 標準 PID | 回転数、車速、水温、吸気温、スロットル開度など | ほぼなし | 低 |
| OBD + UDS 独自 PID | ECU 内部の値（探索が必要） | 読み出しのみなら低い | 中〜高 |
| OBD で診断通信を受信 | 診断機の要求と応答のみ | 低い | 低〜中 |
| 車内配線への分岐接続 | ブロードキャスト全般（推定） | 配線ミスで警告灯や故障の恐れ | 高 |

## 出典

1. [mikrocontroller.net: Merkwürdiges Signal statt CAN-Bus am OBD Port eines Mazda 3](https://www.mikrocontroller.net/topic/543213)
2. [mazda-forum.info: Mazda 3 (BP): Kein CAN-Signal am OBD2-Port?](https://www.mazda-forum.info/threads/mazda-3-bp-kein-can-signal-am-obd2-port.83199/)
3. [commaai/openpilot Wiki: Mazda](https://github.com/commaai/openpilot/wiki/Mazda)
4. [speedsignal: Signalbox Mazda 取付説明書](https://dokus.speedsignal.de/all/B-3450266.pdf)
5. [smartdriving.io: CX-30 接続位置](https://setup-as.smartdriving.io/element.-skhemy-podklyucheniya-dlya-avtomobilei/mazda/cx-30)
6. [mazda3revolution: Mazda Diagnostic Tools and Service Tools info](https://www.mazda3revolution.com/threads/mazda-diagnostic-tools-and-service-tools-info.246787/)
7. [The Ranger Station: FORScan 対応車種の記載](https://www.therangerstation.com/?p=17789)
8. [Mazdas247: AA Torque Puts Engine PIDS on the Monitor Free](https://mazdas247.com/forum/threads/aa-torque-puts-engine-pids-on-the-monitor-free.123882927/page-2)

参考:

- [Torque Interceptor 2 ドキュメント](https://moretorques-organization.gitbook.io/torque-interceptor-2/)（2021年式 Mazda 3 で開発された操舵トルク関連デバイス。CAN ではなく SENT 方式のトルクセンサーに割り込む）
- [LaXiS96/esp32-obd2](https://www.github.com/LaXiS96/esp32-obd2)（Mazda3 の CAN を ESP32 で記録する個人プロジェクト。対象世代は未確認）
