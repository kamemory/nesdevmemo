# MMC3

## バンク

+ CPU $6000-$7FFF: 8KB PRG RAM bank (optional)
+ CPU $8000-$9FFF (or $C000-$DFFF): 8KB switchable PRG ROM bank
+ CPU $A000-$BFFF: 8KB switchable PRG RAM bank (optional)
+ CPU $C000-$DFFF (or $8000-$9FFF): 8KB PRG ROM bank, 最後から2番目のバンクに固定
+ CPU $E000-$FFFF: 8KB PRG RAM bank, 最後のバンクに固定
+ PPU $0000-$07FF (or $1000-$17FF): 2KB switchable CHR bank
+ PPU $0800-$0FFF (or $1800-$1FFF): 2KB switchable CHR bank
+ PPU $1000-$13FF (or $0000-$03FF): 1KB switchable CHR bank
+ PPU $1400-$17FF (or $0400-$07FF): 1KB switchable CHR bank
+ PPU $1800-$1BFF (or $0800-$0BFF): 1KB switchable CHR bank
+ PPU $1C00-$1FFF (or $0C00-$0FFF): 1KB switchable CHR bank

## レジスタ

MMC3には、$8000-$9FFF、$A000-$BFFF、$C000-$DFFF、$E000-$FFFFの4組のレジスタがあります。
偶数アドレス($8000、$8002など)は下位レジスタ、奇数アドレス($8001、$8003など)は上位レジストを選択します。
これらは、メモリマッピング($8000、$8001、$A000、$A001)と、スキャンラインカウンタ($C000、$C001、$E000、$E001)のように、独立した機能ユニットに分割できます。

### バンク選択レジスタ ($8000-$9FFE, 偶数アドレス)

```
7  bit  0
---- ----
CPMx xRRR
|||   |||
|||   +++- Specify
|||          000: R0: 2KB CHR bank at PPU $0000-$07FF (or $1000-$17FF) を選択
|||          001: R1: 2KB CHR bank at PPU $0800-$0FFF (or $1800-$1FFF) を選択
|||          010: R2: 1KB CHR bank at PPU $1000-$13FF (or $0000-$03FF) を選択
|||          011: R3: 1KB CHR bank at PPU $1400-$17FF (or $0400-$07FF) を選択
|||          100: R4: 1KB CHR bank at PPU $1800-$1BFF (or $0800-$0BFF) を選択
|||          101: R5: 1KB CHR bank at PPU $1C00-$1FFF (or $0C00-$0FFF) を選択
|||          110: R6: 8KB PRG ROM bank at $8000-$9FFF (or $C000-$DFFF) を選択
|||          111: R7: 8KB PRG ROM bank at $A000-$BFFF を選択
||+------- MMC3では使用しません。MMC6を参照してください。
|+-------- PRG ROMバンクモード (0: $8000-$9FFF 交換可能
|                                  $C000-$DFFF 最後から2番目バンク固定
|                               1: $C000-$DFFF 交換可能)
|                                  $8000-$9FFF 最後から2番目バンク固定)
+--------- CHAR A12 inversion (0: $0000-$0FFFまで、2個の2KBバンク
                                  $1000-$1FFFは4個の1KBバンク
                               1: $1000-$1FFFまで2個の2KBバンク
                                  $0000-$0FFFは4個の1KBバンク)
```

#### CHRバンク

#### PRGバンク

### バンクデータレジスタ ($8001-$9FFF, 奇数アドレス)

```
7  bit  0
---- ----
DDDD DDDD
|||| ||||
++++ ++++- バンク選択レジスタに書き込まれた最後の値に基づく、新しいバンクの値。
```

MMC3には6つのPRG ROM用アドレス幅しかないため、R6とR7は上位2ビットを無視します。
一部のROMHACKは、大きなPRG ROMを使用するためにR6/R7の8ビットを拡張していますが、これは多くのエミュレータでは意図的にサポートされていません。

### ミラーリングレジスタ ($A000-$BFFE, 偶数アドレス)

```
7  bit  0
---- ----
xxxx xxxM
        |
        +- ネームテーブルのミラーリング (0:垂直ミラーリング、1:水平ミラーリング)
```

このビットは物理的に接続された4画面VRAMを持つカートリッジでは影響ありません。

### PRG RAMプロテクトレジスタ ($A001-$BFFF, 奇数アドレス)

### IRQラッチレジスタ ($C000-$DFFE, 偶数アドレス)

```
7  bit  0
---- ----
DDDD DDDD
|||| ||||
++++ ++++- IRQラッチ値
```

このレジスタは、IRQカウンタのリロード値を定義します。
IRQカウンタが0 (または、$C001からリロードを要求された場合)、この値は、現在のスキャンラインの約260PPUサイクル近辺で、PPUアドレスの次の立ち上がりエッジでIRQカウンタにコピーされます。
※NEXT rising edge of the PPU address意味わからん

### IRQリロードレジスタ ($C001-$DFFF, 奇数アドレス)

```
7  bit  0
---- ----
xxxx xxxx
```

Writing any value to this register reloads the MMC3 IRQ counter at the NEXT rising edge of the PPU address, presumably at PPU cycle 260 of the current scanline.

### IRQ無効レジスタ ($E000-$FFFE, 偶数アドレス)

```
7  bit  0
---- ----
xxxx xxxx
```

このレジスタに何らかの値を書き込むと、MMC3の割り込みが無効になり、保留中の割り込みはacknowledge。

### IRQ有効レジスタ ($E001-$FFFF, 奇数アドレス)

```
7  bit  0
---- ----
xxxx xxxx
```

このレジスタに何らかの値を書き込むと、MMC3の割り込みが有効になります。

## ハードウェア

## IRQ特記事項

## Variants
