# ポケコン SHARP PC-G803

## PC-G803

- CPU Z80互換
- RAM 8KB
- 24列3行 1文字5x7ドット

### メモリマップ

- RAM：0x8000-0x9FFF 8KB?

## モニタ

### 起動方法

- BASICモード(RUNモード or PROモード)で MON
- or アセンブラモード(Shift+BASIC)で、A、CLSボタン

### コマンド

#### USER

- 機械語エリアの確保
    - USER01FF (&H0100-&H01FFまで確保)
- 機械語エリアの開放
    - USER00FF
- 機械語後エリアの表示
    - USER

#### S

- メモリの書き込み
    - S0100 (アドレス省略で&H0100から)
    - 変更なしはリターン
    - 戻すときは CLS または 左
    - BREAKで終了

#### D

- メモリの読み込み
    - D0100,01FF (アドレス省略で&H100から、終了アドレス省略可能)

#### P

- プリントスイッチ
    - P プリントモードとの切り替え (PRINTシンボル)

#### G

- 指定したアドレスからの実行
    - G0100

#### R

- リードSIO (シリアル入力)
    - R0100 (アドレス省略で&H0100)

#### W

- ライトSIO (Intel HEX形式)
    - W0100,01FF

#### BP

- ブレークポイント設定
    - BP011E ブレークポイント設定(2カ所まで、あと2つが有効)
    - 指定アドレスを F7(RST 30H) に設定して実行する
- ブレークポイント表示
    - BP
- ブレークポイント消去
    - BP0

## アセンブラ

### マシン語実行の流れ

1. 機械語モニタでマシン語領域確保
2. テキストエディタでプログラム作成
3. アセンブラでアセンブル
4. 機械語モニタでマシン語実行

### アセンブリ言語

- ; コメント
- ORG 100H 先頭アドレス (省略でORG 100H)
    - ORG 0C00H,0100H C00が先頭アドレスとしてアセンブルして100Hから格納する
- END リスト終了
- : ラベル(6文字まで、行番号の後に空白があるとOPECODE ERROR)
- START EQU 0100H 定数定義
- 四則演算（優先順位なし）
- DB 1byteデータ
- DW 2byteデータ
- DS nbyte確保

### サンプル

1. 機械語モニタ (BASIC)
2. MON
3. USER0200
3. テキストエディタ (TEXT)
4. E
5. 下記を入力
```asm
10 LD HL,110H
20 INC (HL)
30 RET
```
6. アセンブラ (SHIFT+BASIC)
7. A (アセンブル)
8. 機械語モニタ (CLSキー or BASICモードで MON)
9. G100
10. S110
11. 0 (メモリリセット)
12. G100
13. D110 (結果確認、増えて1になっている)

### BASICから呼び出し

```
CA.&H100
PE.&H110
```

## BIOS

- 0030 全レジスタ表示
- BE62 1文字表示 (A:文字, E:列(0-23), D:行(0-3)) *破壊 AF BC HL
- BE5F 記号付き1文字表示 *破壊 AF BC HL
- BFEE 複数文字表示 (A:文字, B:文字数, E:列(0-23), D:行(0-3)) -> C:スクロールした回数, DE:最後の文字の位置 *破壊 AF B HL
- BFF1 文字列表示 (B:文字数, E:列(0-23), D:行(0-3), HL:文字列の先頭アドレス) -> C:スクロールした回数, DE:最後の文字の位置, HL:最後の文字のアドレス *破壊 AF B
- BFD0 縦8ドット パターン表示 1マス5列 (B:パターン長, E:列(0-23), D:行(0-3), HL:パターンの先頭アドレス) 1行120byte *破壊 AF B E
- BFEB 上スクロール *破壊 AF B HL
- BE65 指定位置から右下までを下スクロール (E:列(0-23), D:行(0-3)) *破壊 AF BC DE HL
- BE53 キーコード取得 -> A:キーコード, 入力ありのときキャリーフラグ *破壊 F B BC' DE' HL'

## 11ピン I/Oポート

- OUT 60H,n で切り替え可能
- 18H	11Pin I/Fの出力レベル (Beep)

### mode 0

出力、入力共に３ピン
OUT n / n=INP (n:0-7) じBASICから制御可能

上から順
```
1	-
2	VCC 5.6V
3	GND
4	OUT4
5	OUT2
6	INP4
7	OUT1 (BEEP)
8	INP2
9	INP1
10 -
11 -
```

### mode 1

8bit PIO

- 61H	PIOの入出力方向 (1:入力)
- 62H	PIOデータレジスタ

### mode 2

UART(全二重通信)

上から
```
1	—	—	未使用
2	VCC	—	+5V電源出力
3	GND	—	グランド
4	RTS	出力	“Ready To Receive”（RTSとして表示）
5	DTR	出力	Data Terminal Ready
6	RXD	入力	データ受信用
7	TXD	出力	データ送信用
8	CD	入力	キャリア検出（Carrier Detect）
9	CTS	入力	Clear To Send
10	DSR	入力	Data Set Ready
11	CI	入力	Call Indicator
```
電圧レベルは TTL（0 V/5 V）ですが、論理は 反転（HIGH=idle, LOW=start）仕様 (要、反転対応のUSB‑UARTケーブル（FTDIチップなど）)

- 63H	UARTのフロー制御レジスタ 
- 6CH	UARTモードレジスタ
- 6DH	UARTコマンドレジスタ
- 6EH	UARTステータスレジスタ
- 6FH	UART送受信レジスタ


## reference

- [PC-G800系 IOCSコール](https://ver0.sakura.ne.jp/doc/pcg800iocs.html)
- [SHARP PC-G850 解析資料 : akiyan.com](https://www.akiyan.com/pc-g850_technical_data#bios)
- [ポケコンの部屋 [I/O編]](http://park19.wakwak.com/~gadget_factory/factory/pokecom/io.html)
- [１ドットの誘惑　第４回](https://web.archive.org/web/20051120021931/http://orange.kakiko.com/cosmopatrol/1dot/dot4.html)
- [ポケットコンピュータ - Wikipedia](https://ja.wikipedia.org/wiki/%E3%83%9D%E3%82%B1%E3%83%83%E3%83%88%E3%82%B3%E3%83%B3%E3%83%94%E3%83%A5%E3%83%BC%E3%82%BF)
- [ゲームプログラマの落書き帳：Z80 講座](https://xevi.web.fc2.com/z80/index.html)
