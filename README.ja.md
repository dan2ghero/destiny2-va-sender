# destiny2-va-sender v1.0.6

**動作対象：Windows 11のみ（Works only on Windows 11）**

SRT / RTMPに対応するDestiny 2の映像・プロセス音声送信アプリです。

## 使用例

レイドレースイベントやPvP大会などで、プレイヤーのゲーム映像・音声をイベント運営チームの映像サーバーへ送信します。運営チームは受信した映像を、各プレイヤーの状況確認、視点の切り替え、大会・イベント配信に活用できます。

## 起動と送信

1. ZIPをフォルダごと解凍し、destiny2-va-sender.exeをダブルクリックします。
2. 初回起動時に公式FFmpegを自動取得します (約211 MB)。指定ビルドのSHA-256を照合して配置します。
3. Destiny 2を起動してゲーム画面を表示します。
4. SETTINGでSRTまたはRTMP、映像サイズ・FPS・補正・エンコーダー・音声を選び、SAVEします。
5. OUTPUTに送信先URLを入力してSTART。STOPで終了します。
6. TESTは外部へ送信せず、ffplayで20秒分の映像・音声を確認できます。起動とバッファリングの時間が少し加わります。TEST中もSTOPで終了できます。

## 言語

初期表示は英語です。SETTING → LANGUAGEでENG / JPNを選択し、SAVEを押すとメイン画面に反映されます。選んだ言語はsettings.jsonに保存され、次回起動時にも使用します。JPNを選択中は設定 → 言語 / LANGUAGEから変更できます。FFmpeg・ffplay・Windowsの生ログは元の表記を保持します。

## 設定

| 出力サイズ | 30fps | 60fps |
|---|---:|---:|
| 960x540 | CBR 3 Mbps | CBR 4 Mbps |
| 1280x720 | CBR 4.5 Mbps | CBR 6 Mbps |
| 1920x1080 | CBR 6 Mbps | CBR 8 Mbps |

キーフレーム間隔1秒、Bフレーム0、音声128kbps。初期値はSRT / 1280x720 / 30fps / NVEnc P3 / Opusです。

- SRT: MPEG-TS、AACまたはOpus。srt:// で始まる送信先。
- RTMP: FLV、AAC固定。rtmp:// または rtmps:// で始まる送信先。TESTもFLVです。
- CROP: アスペクト比を維持して中央クロップ。
- COMPRESS: 指定の16:9へ変形。
- LETTER-BOX: 全体を収め、余白を黒で埋める。
- CPU: libx264 / veryfast / zerolatency。
- NVEnc: h264_nvenc / p3 / ll / zerolatency。
- AMD: h264_amf / ultralowlatency / speed。

ffplayの解析上限は1秒・1MiB。nobuffer等の再生オプションは追加していません。

## CUSTOM

CUSTOMは「-オプション 値」の組で映像・音声の出力オプションを指定します。入力・URL・転送形式・フィルター・TEST時間はアプリが管理します。

RTMPではCUSTOMの音声コーデック・ビットレート・チャンネル・サンプルレート指定を置換し、AAC / 128kbps / 48kHz / stereoを1回ずつ設定します。Opus専用オプションは除去します。SRTのCUSTOMは入力内容を使用します。

設定は同じフォルダのsettings.jsonへ保存します。送信先URLは保存しません。旧設定を移行する場合はsettings.jsonを新版フォルダにコピーしてください。

## 動作環境・制限

- Windows 11 x64 / .NET Framework 4.8 (Windows 11標準搭載)。.NET 8 Runtimeの追加インストールは不要です。
- NVIDIA/AMD方式はそれぞれ対応GPUとドライバーが必要です。
- FFmpegはBtbN N-124714-g49a77d37be-20260531を使用します。
- 取得対象はdestiny2.exeとその子プロセスの音声です。
- STOPとアプリ終了では、このアプリが起動したプロセスだけを終了します。
- ネットワーク・サーバー・受信プレイヤーを含む0.5秒以内の遅延は保証・実測確認していません。

## ライセンス

独自実装はThe Unlicenseです（LICENSE参照）。アプリの著作権表記は不要で、利用・解析・改変・fork・商用利用・再配布が可能です。FFmpegなど第三者のソフトウェアはそれぞれのライセンスに従います（licenses/THIRD-PARTY.md参照）。

## ソースとビルド

srcに全ソースを含みます。Windowsのビルド用PCに.NET SDK 8と.NET Framework 4.8を用意し、powershell -ExecutionPolicy Bypass -File .\build.ps1を実行します。NuGetパッケージの復元は不要です。実行ポリシーの指定はそのプロセスだけに適用します。
