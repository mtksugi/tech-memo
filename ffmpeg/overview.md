---
title: FFmpeg メモ
---

# ffmpeg

## mp4→gif変換

画質を落とさず高圧縮で変換

ex:

```bash
ffmpeg -i in.mp4 -filter_complex "[0:v] fps=5,scale=640:-1,split [a][b];[a] palettegen [p];[b][p] paletteuse=dither=none" out.gif
```

## 動画の簡単圧縮

-crfオプション 数値を上げれば画質低で高圧縮

ex.

```bash
ffmpeg -i in.mp4 -crf 32 out.mp4
```

## スケールの変更
-vf scale=800:-1  
-1は縦横比をあわせる

ex.

```bash
ffmpeg -i in.mp4 -vf scale=800:-1  out.mp4
```

## ハードウェアアクセラレータ
- gpuを使って処理するとめちゃくちゃ高速
- 通常はcpuで処理するが -c:v でハードウェアアクセラレータを指定できる
- macOSのvideotoolboxで10倍のスピード

- MacBookProで指定する場合  
-c:v h264_videotoolbox   

```bash
ffmpeg -encoders | grep h264
```
で使用できるh264エンコーダを確認する

- ただし、-c:v のみだとcpuエンコードの画質より劣化するっぽい. ビットレート( -b:v )を指定してビットレートを上げるとよい

ex.

```bash
ffmpeg -i in.mp4 -c:v h264_videotoolbox -b:v 560k out.mp4 
```
